---
layout: post
cover:
title: Deploying a Flask App to AWS
date: 2018-06-05 08:00:00
tags: Metis AWS Flask D3 tutorial
author: Parker
---
## Using Elastic Beanstalk to share your work with the world<br>

For nearly two months now, I have been trying to figure out how to deploy a Flask app to an AWS instance. Because I had only ever set up an EC2 instance, and I was afraid anything else would drain the $1,000 credit I had from Metis, I at some point dismissed the idea as impossible. Well thanks to some of my classmates, I learned about Elastic Beanstalk on AWS, and within a couple of hours I had successfully deployed my first app! I'm writing this tutorial so that others won't even need to spend several hours going through the process, because this process is easy enough that I had my second app deployed in about 15 minutes. I'm going to start with a brief description on how to build a Flask app, so feel free to skip down to the AWS portion if you're already familiar with that process.

*Note: I run Ubuntu 16.04, so I cannot guarantee that this tutorial will work on Mac, Windows, or another deployment of Linux (but it should)*

### Building a Flask App

As little as two months ago, I didn't even understand the difference between HTML and CSS, which I hope will ease your mind on how easy it is to put together a simple Flask App. At its most basic level, a Flask app can simply be a Python call to initiate some HTML template. With three simple files, outlined in the snippets below, you can render a very simple webpage with Python from your command line. The folder structure should be as follows:

```
flask-folder
│   application.py
│
└───templates
│   │   index.html
│   
└───static
    │   style.css
```

Whenever another html template is added, it should go into the templates folder. Other static files, such as images can go wherever you are most comfortable, it is all a matter of preference. I personally like to create a separate folder for images, but it is acceptable to store them in the static folder as well.

Each of the files above should be as follows:

#### application.py
``` python
from flask import(
  Flask, render_template
)

# Initialize the app
application = Flask(__name__)

# Homepage
@application.route("/", methods=['GET','POST'])
def viz_page():
  """
  Homepage: serve our visualization page, index.html
  """
  return render_template('index.html')

# Run the app
if __name__ == "__main__":
  application.run()

```

#### index.html
``` html
  <!doctype html>
  <html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>App Test</title>
    <link rel="stylesheet" href="static/style.css" />
  </head>

  <body>
    <div>
      <h2>Hello World</h2>
      <h3> This is a Flask App! </h3>
      <h4>Well done...</h4>
    </div>
  </body>
```

#### style.css
``` css
  body {
    font-family: Ubuntu;
    background: 	#E8E8E8;
  }

  h2 {
    font-size: 4em;
    color: 	#4e606e
  }
  h3 {
    font-size: 1.5em;
    color: 	#4e606e
  }
  h4 {
    font-size: 1em;
    color: 	#4e606e
  }
```

With the folder structure properly set up as above, you can run the following in your terminal, and it will run your app.

``` bash
 cd flask-folder
 python application.py
```

As I said, this is a very simple app that can be built without too much of a headache, but the same logic in building this can be applied to much more complicated apps. For example, you can enhance the HTML with some JavaScript, add some more style to your CSS, and add some more elaborate code to your Python, but the general outline will remain as above for every Flask app.

### Deploying to AWS

Now that you've built your Flask app, it's time to send it up to the cloud and share it with all your friends and family (even if they're not gonna click it, at least they're nice enough to act interested). Whatever app you're building is going to have some dependencies on Python packages, Flask at a minimum, so you need to create a requirements.txt file for the app. Rather than installing every one of the myriad packages that you've installed on your local Python instance onto your AWS, you'll want to create a requirements file that is specific to the app. To do this, you'll need to create a virtual environment on your local machine for the app. This is done with the following code:

``` bash
  virtualenv ~/flask-venv
  source ~/flask-venv/bin/activate
  pip install flask
  pip install pandas
  pip freeze > requirements.txt
  deactivate
```

The virtual environment "flask-venv" is created with the first line, and then activated with the next line. With the virtual environment up and running, you install your packages as needed, and write your requirements.txt file with pip freeze. It is important to note that scipy is required to run SKLearn, but scipy will not be automatically installed when installing SKLearn. For that reason, make sure you separately install scipy if you require SKLearn for your app. The deactivate at the end simply ends the virtual session and returns you to your local terminal. Now that you have your requirements.txt file written, you're ready to move on to deployment.

The first step is logging on to AWS and navigating to your console. From here, click the Elastic Beanstalk option under the Compute service. Select the Create New Application option in the upper right-hand corner of the page, and come up with a name for your app. Be aware, you can only use a name for an app once, so try not to be too generic with it. This will take you to a page telling you that you currently have no environments, so (you guessed it) you're going to click "Create One Now" in the center of the page. Select the Web server environment for your Flask app, and you'll be taken to the configuration screen. Name your environment and select a domain. Then, select Python from the "Preconfigured Platform" dropdown. Make sure not to select the Docker version of Python, as this needlessly complicates your deployment.

The final step is to upload your code as a zip. Before you zip and deploy to AWS, check on the following three items. First and second, name your .py "application.py" and ensure that the app variable in the file is always application and not app (i.e. application = Flask("\__name\__") rather than app = Flask("\__name\__")), and make sure your CSS folder is named "static". These settings are not absolutely critical, as they can be fixed once the environment has been deployed, but it makes for a more seamless deployment. Finally, make sure that when you zip the file, the top level of the .zip is the inside of flask-folder, not the folder itself. AWS expects to find your requirements.txt and application.py file immediately on opening the zip, so if there is a folder level above these two files it will throw errors. To zip correctly, open your folder, highlight all the files and folders, and zip, rather than zipping the folder itself. With your files properly named and zipped correctly, select the "Upload your code" option and upload your zip. After that, click "Create your Environment", and you'll be taken to a screen that looks a bit like a terminal, and the status of your deployment will be logged.

If you followed these instructions as above, you should have an app up and running in a couple of minutes, but check the Logs in your app console if you're seeing any issues and troubleshoot as needed. If all else fails, feel free to contact me.

Thank you for reading, and check out [the app](http://startupsv3-env.pft3wgfwbq.us-east-2.elasticbeanstalk.com/) (the one I talk about in my last post) I deployed while writing this tutorial!
