# cse112-node-tutorial
CSE 112 NodeJS tutorial

## What is Node.js ? 
Node.js® is a JavaScript runtime built on Chrome's V8 JavaScript engine. Node.js is a runtime environment and not a framework. 

### Install Node
Install Node.js from here https://nodejs.org/en/

Check version from terminal. 
```
$ node -v
v6.9.5
```

Lets us run a basic hello world program and check.

Save the following code in `helloworld.js`

```
console.log("Hello world");
```
and run from the terminal 
```
node helloworld.js 
```

### Express
Express is a fast web framework for Node.js.
Let us install express generator using NPM (Node Package Manager) which is bundled with Node.js. You might need `sudo` permission for the following command. Please check `https://expressjs.com/en/starter/generator.html`

```
$ npm install express-generator -g
```

Let us initialize a project `myapp` using EJS view template engine. 
```
$ express --view=ejs myapp

   create : myapp
   create : myapp/package.json
   create : myapp/app.js
   create : myapp/public
   create : myapp/routes
   create : myapp/routes/index.js
   create : myapp/routes/users.js
   create : myapp/views
   create : myapp/views/index.ejs
   create : myapp/views/error.ejs
   create : myapp/bin
   create : myapp/bin/www
   create : myapp/public/javascripts
   create : myapp/public/images
   create : myapp/public/stylesheets
   create : myapp/public/stylesheets/style.css

   install dependencies:
     $ cd myapp && npm install

   run the app:
     $ DEBUG=myapp:* npm start


```
and as per instructions run the commands

```
$ cd myapp && npm install
$ DEBUG=myapp:* npm start
```

Now go to `http://localhost:3000/` in the browser and you see welcome message for Express.

![express_welcome](https://github.com/sujithvm/cse112-node-tutorial/blob/master/readme_images/express_welcome.png)

### Let us push the code to Github

1. Make a new repository and choose gitignore for Node. `gitignore` has files that you want to exclude. 
2. Once the repository is created, run the following commands. Please the `git` tutorial for more information.

```
$ git init

$ git remote add origin https://github.com/sujithvm/cse112-node-tutorial.git

$ git pull origin master
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (4/4), done.
From https://github.com/sujithvm/cse112-node-tutorial
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> origin/master

$ git add .

$ git commit -m 'initial setup'
[master 4fc5eda] initial setup
 8 files changed, 193 insertions(+)
 create mode 100644 app.js
 create mode 100755 bin/www
 create mode 100644 package.json
 create mode 100644 public/stylesheets/style.css
 create mode 100644 routes/index.js
 create mode 100644 routes/users.js
 create mode 100644 views/error.ejs
 create mode 100644 views/index.ejs

$ git push origin master
Counting objects: 15, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (12/12), done.
Writing objects: 100% (15/15), 2.69 KiB | 0 bytes/s, done.
Total 15 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), done.
To https://github.com/sujithvm/cse112-node-tutorial.git
   e515093..4fc5eda  master -> master

```

### Let us write a simple test using `Mocha` .
`Mocha` is JavaScript test framework running on Node.js

```
npm install --save mocha
```

Make a test directory

```
mkdir test
```

Create a `test.js` file in `test` directory - `test/test.js` and enter the sample code.

```
var assert = require('assert');
describe('Array', function() {
  describe('#indexOf()', function() {
    it('should return -1 when the value is not present', function() {
      assert.equal(-1, [1,2,3].indexOf(4));
    });
  });
});
```

Now let us add `"test": "mocha"` to the `package.json` scripts 

```
"scripts": {
    "start": "node ./bin/www",
    "test": "mocha"
  }
```

And run

```
$ npm test
```

You should now see the test case run 

```
Array
    #indexOf()
      ✓ should return -1 when the value is not present

  1 passing (7ms)
```


Let us update Github with the updated code.

```
$ git add .

$ git commit -m 'add test'

$ git push origin master
```


### Let us add a REST API function

Make route for `/hello` . Make new file in `routes/hello.js` 

```
var express = require('express');
var router = express.Router();

/**
 * @api {get} /hello Respond back name passed as query
 * @apiName Ping Name
 *
 * @apiParam {String} name of the user.
 *
 * @apiSuccess {Boolean} err error if occured.
 * @apiSuccess {String} message status message.
 * @apiSuccess {String} name query name.
 */
router.get('/', function(req, res, next) {
   if (req.query.name) {
      return res.json({
         'err' : false,
         'message' : 'name found in query',
         'name' : req.query.name
      })
   } else {
      return res.json({
         'err' : true,
         'message' : 'no name found.',
         'name' : ''
      })
   }
});

module.exports = router;

```

Now update the `app.js` file. 

```

var hello = require('./routes/hello');
...
...
app.use('/hello', hello);

```

Run the server.

```
$ DEBUG=myapp:* npm start
```

Let us check the route from Postman is a great tool for testing APIs. Postman chrome extension / app is available.

![postman](https://github.com/sujithvm/cse112-node-tutorial/blob/master/readme_images/postman.png)

### Build tool Gulp

Gulp is a build tool for automating tasks. Now we shall see how we add a lint checker, run mocha tests and make documentation.

Let us install the dependencies. 

```
npm install --global gulp-cli

npm install --save gulp gulp-apidoc jshint gulp-jshint gulp-mocha 
```

Now create a `gulpfile.js` 

```
var gulp = require('gulp'),
   jshint = require('gulp-jshint'),
    apidoc = require('gulp-apidoc'),
    mocha = require('gulp-mocha');

/**
* Lint Checker
*/
gulp.task('lint', function () {
   gulp.src('./**/*.js')
      .pipe(jshint())
})

/**
* Run Mocha Tests
*/
gulp.task('mocha', () =>
   gulp.src('test/test.js', {read: false})
      .pipe(mocha({reporter: 'nyan'}))
);

/**
* Run documentation generator
*/
gulp.task('apidoc', function(done){
   apidoc({
      src: "routes/",
      dest: "doc/"
   }, done);
});

gulp.task('default', ['lint', 'mocha', 'apidoc']);


```

and run 


```
$ gulp
```

The documentation for the API will be outputted to `/doc`.  Open `index.html` to view documentation. Configure `apidoc.json` as specified here `http://apidocjs.com/` for more customizations.


![documentation](https://github.com/sujithvm/cse112-node-tutorial/blob/master/readme_images/documentation.png)


### Continuous Integration 

Activate project on Travis 

![travis_activate](https://github.com/sujithvm/cse112-node-tutorial/blob/master/readme_images/travis_activate.png)

Now make a `.travis.yml` file (Please note the dot . )

```
touch .travis.yml
```

```
language: node_js

node_js:
  - "6"

before_script:
  - npm install -g gulp

script: gulp

```

Now simply push to Github, Travis CI build automatically be started. 

```
$ git add .
$ git commit -m 'travis integration'
$ git push origin master

```

![travis_build](https://github.com/sujithvm/cse112-node-tutorial/blob/master/readme_images/travis_build.png)

### Deployment

#### Please make a new project in Heroku 

Install Heroku CLI

```
brew install heroku

```

Install Travis CLI 

```
$ sudo gem install travis
```

Setup travis 

```
$ travis setup heroku
```

Now you will see that a `secure` key is added to `.travis.yml`

Now simply push the project, travis build will automatically start and if successful Heroku build will start and be deployed.

```
$ git add .
$ git commit -m 'deploying'
$ git push origin master
```

Now go to the Heroku app `https://cse112-node-tutorial.herokuapp.com/hello?name=sujith` . 

Congratulations! 
