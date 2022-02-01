---
layout: post
title:  "Big-Data-Processing (FullStack & beyond) Part-1"
date:   2022-01-31
categories: Blog Data-Engineering Big-Data Full-Stack  
---

# Web-App to capture/generate data

In this post, let's go over the creation of web application to capture user behavior. ```Docker``` and `compose` are required if you want to simulate the work. 

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

Save this into a file named ```Dockerfile``` and run,

```
docker build -t my-angular .
docker run --name angular --rm -it -p 4000:4000 -v $PWD:/usr/local/bin/angular my-angular bash 
```

We have mounted the ```current directory``` as ```/usr/local/bin/angular``` in our docker container so as to persist our work. Later, we will use ```compose``` to bring up the whole environment in a single command. 

Let's generate our app scaffolding with,

{% highlight bash %}
cd /usr/local/bin/angular
ng new imdb-app --routing=true --style=scss
{% endhighlight %}

Now, run the application using,
{% highlight bash %}
cd /usr/local/bin/angular/imdb-app
echo '<h1>Hello, World!</h1>' >src/app/app.component.html 
ng serve --watch --port 4000 --host 0.0.0.0
{% endhighlight %}

In your web browser, visiting <a>http://localhost:4000</a> should show a ```Hello, World!``` message.

We will be editing the files in ```<your current directory/imdb-app/``` below. You should see this webpage refresh automagically as you make edits to these files.

Let's get to work to add a form so that user can enter ```movie``` and/or ```actor``` on the webpage. We'll edit the ```app.component.(html/ts)``` files. Once we are done, the page will look as showed below.

![ui-a](/assets/images/big-data-part-1-ui-a.png)

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


