---
layout: post
title:  "Doing it Live: CSS Preprocessing and JS Hinting"
subtitle: "Processing Sass, LESS, and JavaScript with Node.js"
date:   2014-02-02 21:38:21
---


<p><em>This is an illustration of a concept, this is not a tutorial and is not even close to production-ready code. Use this as a jumping-off point for actual good code. If you just want to jump into the code, <a href="https://github.com/frxnz/node-preprocessor">head to GitHub</a></em></p>

## If you edit code in the browser, you might like this.

<p>If you&rsquo;re a <a href="http://codepen.io">CodePen</a> user you&rsquo;ve seen this before. You&rsquo;re editing code in the browser, typing away, and then you pause for a second and your Sass or LESS magically turns into compiled CSS. Or perhaps you&rsquo;re writing some JavaScript, and not doing a great job, and boom there&rsquo;s your JSHint error message. If you&rsquo;re not a CodePen user, you should be, but you still might find this helpful.</p>

<p>How does this happen? If we open up the dev tools and take a peak at the network panel on CodePen, we get some clues.</p>

<p><img src="/img/codepen-preprocessors.png" alt="" /></p>

<p>Every time you stop typing you&rsquo;ll see that <code>preprocessors</code> entry, and if you look at the response you&rsquo;ll see that (at least in this case) it responded with some compiled CSS. Essentially what&rsquo;s happening is this: after you&rsquo;ve stopped typing a POST is sent to the server, containing the pre-compiled CSS and a property to let the server know which compiler to use. The server compiles the Sass or LESS or whatever, and sends back the compiled CSS.</p>

<p>CodePen happens to use Ruby for this, but I&rsquo;m going to explain how you might do it using Node. I&rsquo;m also going to assume you know a bit about Node&mdash;including how to set up an Express server, and how routing works in Express.</p>

<h2>The Server</h2>

<p>The way I&rsquo;m going to illustrate this requires a few things:</p>

<ol>
<li>An Express server</li>
<li><code>app.use(express.json())</code></li>
<li>Two GET routes</li>
</ol>


<p>I&rsquo;m sure there are plenty of other ways to configure a server to do this, but this is what I&rsquo;ll be using.</p>

<p><code>express.json()</code> is necessary because we will be returning our data as JSON. The GET routes are necessary because we will be calling the methods via AJAX. This is what you&rsquo;ll need in your server:</p>

<pre title="Express Server"><code class="language-javascript">
// This will go with the rest of yout middleware config.
// In this case app = express(), adjust for your server.
app.use(express.json());

// These are our API routes. 'api' refers to the file
// you'll be exporting your API methods from. In my case
// api = require('./routes/api'),
app.get('/api/jshint',     api.jshint);
app.get('/api/preprocess', api.preprocess);
</code></pre>


<p>You will probably need to adjust a few things in there to work with your server, but that should give you the general idea.</p>

<h2>The API Methods</h2>

<p>For the <code>./routes/api</code> module you&rsquo;ll need to require a few dependencies. LESS and JSHint are both written in JavaScript, so we&rsquo;re using their official Node modules. Sass is written in Ruby, so we&rsquo;re using a Node port, which is actually based on libsass. libsass is very actively maintained and should work for most purposes.</p>

<pre title="API Dependencies"><code class="language-javascript">
var sass   = require('node-sass'),
    less   = require('less');
    jshint = require('jshint');
</code></pre>


<h3>JSHint</h3>

<p>There&rsquo;s not a lot to the JSHint method. This endpoint will return a <code>data</code> object containing an array of error objects. There are a number of options that can be changed, but we&rsquo;ll just stick with the defaults for now.</p>

<pre title="JSHINT Method"><code class="language-javascript">
exports.jshint = function(req, res) {
    var // The actual hinting is done using a method called JSHINT
        hint = jshint.JSHINT,

        // The code that we're hinting will be passed in as a querystring
        code = req.query.js;

        // This will run hint (jshint.JSHINT) on our code.
        // If there are errors, our response will be hint.errors
        // otherwise the resonse will be null
        response = !hint(code) ? hint.errors : null;

    // This will send the JSHint response back to the requester as json
    res.jsonp({ "data" : response });
};
</code></pre>


<p>The response, if there are errors, will look something like this:</p>

<pre title="JSHINT Response"><code class="language-javascript">
{
    data: [
        {
            id: "(error)",
            raw: "Missing semicolon.",
            code: "W033",
            evidence: "var foo=bar",
            line: 1,
            character: 13,
            scope: "(main)",
            reason: "Missing semicolon."
        }
    ]
}
</code></pre>


<h3>CSS Preprocessors</h3>

<p>For Sass and LESS we&rsquo;re only using one route. This way if your code editor supports multiple preprocessors, the burden of routing is placed on the server rather than the requester. The preprocessor will be passed as a querystring parameter, and the server will handle the rest.</p>

<p>The <code>preprocess</code> route works more or less the same way as the <code>jshint</code> route. It takes in code as a querystring parameter, and returns JSON as the response. Similarly, if there is an error in the code, this route will return an <code>error</code> object.</p>

<p>Unlike the <code>jshint</code> route, our <code>preprocess</code> route actually returns processed CSS in a <code>data</code> object if there are no errors.</p>

<pre title="Preprocessor Config"><code class="language-javascript">
exports.preprocess = function(req, res) {

    var // First let's grab the querystring parameters
        // This is how we will determine which module to use
        lang = req.query.lang,

        // This is the Sass/LESS we're going to process
        code = req.query.css;

    if (lang === 'sass') {
        // sass.render will process the code we send it,
        // If there are errors it will return them,
        // otherwise it will return compiled CSS
        sass.render({
            data: code,
            success: function(css){
                res.jsonp({ "data" : css });
            },
            error: function(error) {
                res.jsonp({ "error" : error });
            },
            // This accepts the same output styles as the
            // Ruby compiler
            outputStyle: 'compressed'
        });
    }

    if (lang === 'less') {
        // less.render takes in the code, and a callback function.
        // If error is defined, we will return an error object,
        // otherwise we will return the compiled CSS
        less.render(code, function (error, css) {
            if (error) {
                res.jsonp({ "error" : error });
            } else {
                res.jsonp({ "data" : css });
            }
        });
    }

};
</code></pre>


<h3>Putting it all together</h3>

<p>Your <code>./routes/api.js</code> file should look like this:</p>

<pre title="./routes/api.js"><code class="language-javascript">
var sass   = require('node-sass'),
    less   = require('less');
    jshint = require('jshint');

exports.jshint = function(req, res) {
    var hint     = jshint.JSHINT,
        code     = req.query.js;
        response = !hint(code) ? hint.errors : null;

    res.jsonp({ "data" : response });
};

exports.preprocess = function(req, res) {

    var lang = req.query.lang,
        code = req.query.css;

    if (lang === 'sass') {
        sass.render({
            data: code,
            success: function(css){
                res.jsonp({ "data" : css });
            },
            error: function(error) {
                res.jsonp({ "error" : error });
            },
            outputStyle: 'compressed'
        });
    }

    if (lang === 'less') {
        less.render(code, function (error, css) {
            if (error) {
                res.jsonp({ "error" : error });
            } else {
                res.jsonp({ "data" : css });
            }
        });
    }

};
</code></pre>


<h2>Using The Thing</h2>

<p>On the client-side, you&rsquo;ll be making AJAX calls to the server we just set up as as the user types. If you&rsquo;re building something like CodePen you would either display the errors, or use the compiled CSS to render a preview. If you were writing a CMS with a code editor, you would display the errors or hold onto the compiled CSS to be stored in a database when the user saves.</p>

<p>This is an extremely basic demo of the concept, but in an actual production environment the performance should be quite good. Doing a round-trip every time a user stops timing might seem a little crazy, but you&rsquo;re not really transferring a lot of data. CodePen does it with over 100,000 users creating thousands of Pens per day.</p>

<h2>Clone it! Fork it! Whatever!</h2>

<p>If you just want to play around with this, you can <a href="https://github.com/frxnz/node-preprocessor">clone my GitHub repo</a> and follow the installation instructions to get it running.</p>
