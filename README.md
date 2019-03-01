# serverlessLambda
How to create serverless


This projected is created by using this Document

https://hackernoon.com/a-crash-course-on-serverless-with-node-js-632b37d58b44

1. First of all you need to install Serverless globally.
Fire up a terminal window and run:

$ npm install -g serverless


You’ve now installed the Serverless framework globally on your machine. The Serverless commands are now available to you from wherever in the terminal.

Note: If you’re using Linux, you may need to run the command as sudo.


2. Create an IAM User in your AWS Console
Open up your AWS Console and press the services dropdown in the top left corner. You’ll see a ton of services show up. Go ahead and write IAM in the search box and press on it.



You’ll be redirected to the main IAM page for your account. Proceed to add a new user.
Pick a funky name for your new IAM user and give the user programmatic access. Proceed to the next step.

Now you can add a set of permissions to the user. Because we are going to let Serverless create a delete various assets on our AWS account go ahead and check AdministratorAccess.

Proceeding to the next step you will see the user was created. Now, and only now will you have access to the users Access Key ID and Secret Access Key. Make sure to write them down or download the .csv file. Keep them safe, don’t ever show them to anybody. I’ve pixelized them even though this is a demo, to make sure you understand the severity of keeping them safe.


With that done we can finally move on to entering the keys into the Serverless configuration.

3. Enter IAM keys in the Serverless configuration
Awesome! With the keys saved you can set up Serverless to access your AWS account. Switch back to your terminal and type all of this in one line:

$ serverless config credentials --provider aws --key xxxxxxxxxxxxxx --secret xxxxxxxxxxxxxx
Hit enter! Now your Serverless installation knows what account to connect to when you run any terminal command. Let’s jump in and see it in action.

4. Create your first service
Create a new directory to house your Serverless application services. Fire up a terminal in there. Now you’re ready to create a new service. What’s a service you ask? View it like a project. But not really. It's where you define AWS Lambda Functions, the events that trigger them and any AWS infrastructure resources they require, all in a file called serverless.yml.

Back in your terminal type:

$ serverless create --template aws-nodejs --path my-service
The create command will create a new service. Shocker! But here’s the fun part. We need to pick a runtime for the function. This is called the template. Passing in aws-node will set the runtime to Node.js. Just what we want. The path will create a folder for the service. In this example, naming it my-service.

5. Explore the service directory with a code editor
Open up the my-service folder with your favorite code editor. There should be three files in there. The serverless.yml contains all the configuration settings for this service. Here you specify both general configuration settings and per function settings. Your serverless.yml looks like this, only with a load of comments.

# serverless.yml
service: my-service
provider:
  name: aws
  runtime: nodejs6.10
functions:
  hello:
    handler: handler.hello
The functions property lists all the functions in the service. You can see hello is the only function currently in the handler.js file. The handler property points to the file and module containing the code you want to run in your function. By default this handler file is named handler.js. Very convenient indeed.

Opening up the handler.js you’ll see the handler module and function named hello. The function takes three parameters. The event parameter represents the event data passed to the function. The context tells us about the context of the function, it’s running time, state and other important info. The last parameter is a callback function which will send data back. In this example the response is sent back as the second parameter of the callback function. The first always represents an error. If there is no error null is passed along.

// handler.js
module.exports.hello = (event, context, callback) => {
  const response = { statusCode: 200, body: 'Go Serverless!' };
  callback(null, response);
};
This is all great, but we still can’t trigger the function. There is no event connected to it, hence no way to trigger the function. Let’s fix this. Jump back to the serverless.yml and uncomment the lines where you see events: .

# serverless.yml
service: my-service
provider:
  name: aws
  runtime: nodejs6.10
functions:
  hello:
    handler: handler.hello
    events: # uncomment these lines
      - http:
          path: hello/get
          method: get
Watch out so you don’t mess up the indentation of the file, events should be directly beneath handler. Great, with that done we can finally deploy the function to AWS.

6. Deploying to AWS
The deployment process is very straightforward. Within the service directory run this command in your terminal:

$ serverless deploy -v
You’ll see the terminal light up with a ton of messages. That’s the -v doing its magic. Gotta love those verbose logs!

But, most important for us is that it will log back the endpoint. Serverless has automagically created an API Gateway endpoint and connected it to the Lambda function. How awesome is that!? Hitting the endpoint in the browser will send back the text Go Serverless!

Note: If you want to test the function through the command line you can run:

$ serverless invoke -f hello -l
This will return back the full response object as well as info regarding the state of the Lambda function, such as duration and memory usage.
