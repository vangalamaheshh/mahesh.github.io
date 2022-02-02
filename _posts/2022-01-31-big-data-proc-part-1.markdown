---
layout: post
title:  "Big-Data-Processing (FullStack & beyond) Part-1"
date:   2022-01-31
categories: Blog Data-Engineering Big-Data Full-Stack  
---

## Web-App to capture/generate data

In this post, let's go over the creation of web application to capture user behavior. ```Docker``` and `compose` are required if you want to simulate the work. 

# Web-Client

{% highlight docker %}
FROM ubuntu:21.04 
# NodeJs v14x or v16x doesn't support Ubuntu v22.04 yet.
RUN set -ex \
    && apt-get update -y \
    && apt-get install -y curl dialog net-tools \
    && curl -sL https://deb.nodesource.com/setup_16.x | bash - \
    && apt-get update -y \
    && apt-get install -y nodejs \
    && npm install --global @angular/cli@latest
# A foreground process to keep the container alive
CMD ["tail -f /dev/null"]
{% endhighlight %}

Save this into a file named ```angular.Dockerfile``` and run,

```
docker build -t my-angular -f angular.Dockerfile .
docker run --name angular --rm -it -p 4000:4000 -v $PWD:/usr/local/bin/imdb-app my-angular bash 
```

We have mounted the ```current directory``` as ```/usr/local/bin/imdb-app``` in our docker container so as to persist our work. Later, we will use ```compose``` to bring up the whole environment in a single command. 

Let's generate our app scaffolding with,

{% highlight bash %}
cd /usr/local/bin/imdb-app
ng new angular --routing=true --style=scss
{% endhighlight %}

Now, run the application using,
{% highlight bash %}
cd /usr/local/bin/imdb-app/angular
echo '<h1>Hello, World!</h1>' >src/app/app.component.html 
ng serve --watch --port 4000 --host 0.0.0.0
{% endhighlight %}

In your web browser, visiting <a>http://localhost:4000</a> should show a ```Hello, World!``` message.

We will be editing the files in ```<your current directory/imdb-app/angular``` below. You should see this webpage refresh automagically as you make edits to these files.

Let's get to work to add a form so that user can enter ```movie``` and/or ```actor``` on the webpage. We'll edit the ```app.component.(html/ts)``` files. Once we are done, the page will look as showed below.

![ui-a](/assets/images/big-data-part-1-ui-a.png)

We are going to use ```Angular Material``` to style our webpage, so let's go ahead and install it first. Inside the docker container run,

{% highlight bash %}
ng add @angular/material
{% endhighlight %}

Add the following ```Material``` modules to ```src/app/app.module.ts``` file.

{% highlight javascript %}
// ...
import {FormsModule, ReactiveFormsModule} from '@angular/forms';
import {MatFormFieldModule} from '@angular/material/form-field';
import {MatInputModule} from '@angular/material/input';
import {MatIconModule} from '@angular/material/icon';
import {MatCardModule} from '@angular/material/card';
// ...
imports: [
    BrowserModule,
    //....
    BrowserAnimationsModule,
    FormsModule,
    ReactiveFormsModule,
    MatFormFieldModule,
    MatInputModule,
    MatIconModule,
    MatCardModule
  ],
//...
{% endhighlight %}

Add the following code to ```app.component.ts``` and ```app.component.html``` files, respectively.

{% highlight javascript %}
// src/app/app.component.ts
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent implements OnInit {

  public msg: string = "Big-Data-Demo using IMDB Dataset";
  public actor: string = "";
  public movie: string = "";

  constructor() { }

  ngOnInit(): void { }

  public formSubmit(form: any) {
    /* To Do */
    // Post <form values> to the IMDB server endpoint 
  }
}
{% endhighlight %}

{% highlight html %}
// src/app/app.component.html
<h1 style="text-align: center;">{{msg}}</h1>
<form #form = "ngForm" (ngSubmit) = "formSubmit(form.value)" >
<mat-card style="text-align: center;">
    <mat-card-content>
        <mat-form-field appearance="outline">
            <mat-label>Actor Name</mat-label>
            <input matInput type="text" name = "actor" placeholder="Actor Name ..." ngModel>
            <mat-icon matSuffix>recent_actors</mat-icon>
            <mat-hint>Example: Brad Pitt</mat-hint>
        </mat-form-field><br/>
        <p>and/or</p>
        <mat-form-field appearance="outline">
            <mat-label>Movie Name</mat-label>
            <input matInput type="text" name = "movie" placeholder="Movie Name ..." ngModel>
            <mat-icon matSuffix>movie</mat-icon>
            <mat-hint>Example: Pulp Fiction</mat-hint>
        </mat-form-field><br/>
        <button mat-raised-button color="primary" style="margin-right:5px;">Submit</button>
	    <button type="reset" mat-raised-button color="primary" style="margin-right:5px;">Reset</button>
    </mat-card-content>
</mat-card>
</form>
{% endhighlight %}

<b>Disclaimer:</b> You should be using <a href="https://angular.io/api/forms/FormControl" target="_blank">Form Controls</a>, <a href="https://angular.io/api/forms/FormGroup" target="_blank">Form Groups</a> and <a href="https://angular.io/guide/form-validation" target="_blank">Form Validations</a> for a production app. We are only covering the bare essentials to get the big picture going.

Okay, so with that we've got our UI all set so as to gather input parameters required to fetch the actor/movie information from IMDB dataset we have downloaded <a href="https://vangalamaheshh.github.io/blog/data-engineering/big-data/full-stack/2022/01/30/big-data-proc-overview.html" target="_blank">as explained in this post</a>.

Now, it's time to bring our server side application to life and serve IMDB actor/movie information based on user input.

# Web-Server

{% highlight docker %}
FROM python:3.9.10-slim-buster
RUN set -ex \
    && apt-get update -y \
    && pip install graphene flask flask-cors \
    && pip install flask-graphql pandas
# a foreground process to keep the container alive
CMD ["tail -f /dev/null"]
{% endhighlight %}

Save this into a file named ```flask.Dockerfile``` and run,

{% highlight python %}
docker build -t my-flask -f flask.Dockerfile .
docker run --name flask --rm -it -p 8080:8080 -v $PWD:/usr/local/bin/imdb-app my-flask bash 
mkdir /usr/local/bin/imdb-app/flask && cd $_
cat <<EOF 1>server.py
#!/usr/bin/env python

from flask import Flask, request
from flask_cors import CORS
import json

app = Flask(__name__)

cors = CORS(app, resources = {
  r"/*": {
    "origins": [
      "http://localhost:4200"
    ]}
  })

app.debug = True

@app.route("/")
def hello_world():
  return '<h1>Hello, World!</h1>', 200

@app.route("/FetchInfo", methods = ['POST'])
def fetch_info():
  actor = request.form.get('actor', None)
  movie = request.form.get('movie', None)
  return json.dumps({
    "error": None,
    "msg": None,
    "data": {'actor': actor, 'movie': movie}
  }), 200

if __name__ == "__main__":
  app.run(debug=True, host="0.0.0.0", port="8080")
EOF
# Run flask server
python server.py
{% endhighlight %}
 
Pointing your web browser to <a>http://localhost:8080/</a>, you should see ```Hello, World!``` message. 

Happy Coding! :+1:

<a href="https://www.buymeacoffee.com/MaheshVangala" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>

<div id="share-bar">

    <h4 class="share-heading">Liked it? Please share the post.</h4>

    <div class="share-buttons">
        <a class="horizontal-share-buttons" target="_blank" href="https://www.facebook.com/sharer/sharer.php?u=https://vangalamaheshh.github.io{{page.url}}" 
            onclick="gtag('event', 'Facebook', {'event_category':'Post Shared','event_label':'Facebook'}); window.open(this.href, 'pop-up', 'left=20,top=20,width=500,height=500,toolbar=1,resizable=0'); return false;"
            title="Share on Facebook" >
            <span class="icon-facebook2">Facebook</span>
        </a>
        <a  class="horizontal-share-buttons" target="_blank" href="https://twitter.com/intent/tweet?text=Big Data Processing (FullStack & beyond) - Overview&url=https://vangalamaheshh.github.io{{page.url}}"
            onclick="gtag('event', 'Twitter', {'event_category':'Post Shared','event_label':'Twitter'}); window.open(this.href, 'pop-up', 'left=20,top=20,width=500,height=500,toolbar=1,resizable=0'); return false;"
            title="Share on Twitter" >
            <span class="icon-twitter">Twitter</span>
        </a>
        <a  class="horizontal-share-buttons" target="_blank" href="http://www.reddit.com/submit?url=https://vangalamaheshh.github.io{{page.url}}"
          onclick="gtag('event', 'Reddit', {'event_category':'Post Shared','event_label':'Reddit'}); window.open(this.href, 'pop-up', 'left=20,top=20,width=900,height=500,toolbar=1,resizable=0'); return false;"
          title="Share on Reddit" >
          <span class="icon-reddit">Reddit</span>
        </a>
        <a  class="horizontal-share-buttons" target="_blank" href="https://www.linkedin.com/shareArticle?mini=true&url=https://vangalamaheshh.github.io{{page.url}}"
          onclick="gtag('event', 'LinkedIn', {'event_category':'Post Shared','event_label':'LinkedIn'}); window.open(this.href, 'pop-up', 'left=20,top=20,width=500,height=500,toolbar=1,resizable=0'); return false;"
          title="Share on LinkedIn" >
          <span class="icon-linkedin">LinkedIn</span>
        </a>
    </div>

</div>
<style type="text/css">
/* Share Bar */
#share-bar {
    font-size: 20px;
    border: 3px solid #7de77b;
    border-radius: 0.3em;
    padding: 0.3em;
    background: rgba(125,231,123,.21)
}

.share-heading {
    margin-top: 0px;
}

/* Title */
#share-bar h4 {
    margin-bottom: 10px;
    font-weight: 500;
}

/* All buttons */
.share-buttons {
}

.horizontal-share-buttons {
    border: 1px solid #928b8b;
    border-radius: 0.2em;
    padding: 0.2em;
    margin-right: 0.2em;
    line-height: 2em;
}

/* Each button */
.share-button {
    margin: 0px;
    margin-bottom: 10px;
    margin-right: 3px;
    border: 1px solid #D3D6D2;
    padding: 5px 10px 5px 10px;
}
.share-button:hover {
    opacity: 1;
    color: #ffffff;
}

/* Facebook button */
.icon-facebook2 {
    color: #3b5998;
}

.icon-facebook2:hover {
    background-color: #3b5998;
    color: white;
}

/* Twitter button */
.icon-twitter {
    color: #55acee;
}
.icon-twitter:hover {
    background-color: #55acee;
    color: white;
}

/* Reddit button */
.icon-reddit {
    color: #ff4500;
}
.icon-reddit:hover {
    background-color: #ff4500;
    color: white;
}

/* Hackernews button */
.icon-hackernews {
    color: #ff4500;
}

.icon-hackernews:hover {
    background-color: #ff4500;
    color: white;
}

/* LinkedIn button */
.icon-linkedin {
    color: #007bb5;
}
.icon-linkedin:hover {
    background-color: #007bb5;
    color: white;
}

</style>


