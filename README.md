browse
==========
[Node.js] browsing urls with cookies, that is, we can scrape with authenticated pages!

### Installation ###
    git clone git://github.com/shinout/browser.git

    OR

    npm install browser 

### Usage ###
#### helloworld ####
    var $b = require("browser");
    $b.browse('https://accounts.google.com/Login'); // browse this url
    $b.run(); // execution

#### login sample (requires jquery) ####
    var userdata = {
      email: "XXXXXX@gmail.com",
      pass : "XXXXXXXX"
    };

    var $b = new jbrowser();

    // $b.browse(the label of this request, url to access)
    $b.browse('login', 'https://accounts.google.com/Login');

    /* $b.browse(function(
     *   err : errors occured in the previous request, 
     *   out : result of the previous browsing
     *) { return url or return [url, options] }
     */
    $b.browse(function(err, out) {
      var window = jsdom(out.text).createWindow();
      var $ = jquery.create(window);
      var postdata = {
        Email  : userdata.email,
        Passwd : userdata.pass
      };
      var url = $("#gaia_loginform").attr("action");
      // get hidden fields, and register them to post data
      $("input").each(function(k, el) {
        var $el = $(el);
        var name = $el.attr("name"), type = $el.attr("type"), val = $el.val();
        if (type == "hidden" || type == "submit") postdata[name] = val;
      });
      return [url, {
        data  : postdata, // set post data
        method: "POST"    // set HTTP method (default: GET)
      }];
    })
    .after("login"); // browse after browsing with label="login"

    $b.browse('https://mail.google.com/mail/u/0/?ui=html&zy=d')
    .after(); // browse after previously registered function

    /* running on end of all browsings
     *   err: error object or null
     *   out: { text: result body, ...}
     */
    $b.on("end", function(err, out) {
      if (!out || !out.text) return res.end("no result");
      res.writeHead(200, {'Content-Type' : 'text/html'});
      console.log("result", out.text);
      res.end(out.text);
    });
    $b.run();


#### options object ####
>option object to pass to $b.browse() is the same format as u2r options.
See <a href="https://github.com/shinout/u2r">u2r</a> in detail.
The following are common options.

 - data   : (object) key-value pairs to pass to server
 - method : HTTP method (GET|POST|PUT|DELETE|HEAD). default: GET

>all other values below are automatically generated from URL

 - host
 - protocol
 - path
 - port
 - body : querystring format of options.data

#### keys of out object ####
 - text: response text
 - statusCode
 - location
 - responseHeaders
 - cookies : set-cookie headers
 - url : browsed url
