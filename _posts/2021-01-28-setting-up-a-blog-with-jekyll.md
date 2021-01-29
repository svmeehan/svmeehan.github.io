---
layout: post
title:  "Setting up Jekyll on Github pages"
date:   2021-01-28 17:00:04 +0000
categories: docker jekyll
---
## Introduction

[Jekyll](https://jekyllrb.com) is a blogging software that produces a static webpage. A static webpage is simply one that serves fixed html files. The webpage will never change unless the html files are edited. A dynamic webpage on the other hand can retrieve information from a database and inject the result into a html page before serving it. Think of your google search results. The page always looks the same but the content between the header and footer is unique. Jekyll gives you the dynamic features you need - I don't have to add every new post I create to the main page - but every page is then built into a static html file.

You can check out the install instructions for a local installation [here](https://jekyllrb.com/docs/).

I decided to containerize the application as this offers a few advantages but mainly as I like to keep my personal laptop free from packages I don't use often (apologies to ruby fans!). In this post I will discuss the Dockerfile but if you want to get this up and running feel free to skip the deployment section

## Building the Dockerfile

``` dockerfile
FROM ruby:2-alpine

RUN apk add gcc g++ make && gem install bundler jekyll && apk del gcc g++ make

EXPOSE 4000

RUN jekyll new my-blog

WORKDIR my-blog

CMD bundle exec jekyll serve --host 0.0.0.0
```

Let's briefly discuss the Dockerfile and the choices that were made in order to get it working. The first choice we made was what image to use. Now I didn't want to make my container from scratch (literally `FROM scratch` in Docker) so I took an official ruby image. I used the tag alpine so I could get a smaller version of Linux in order to minimize the final image size.  Originally, I had used `FROM ruby:alpine` which included the latest version of ruby (3.0). Unfortunately, there was a compatibility issue with this version of ruby and jekyll. Docker saved me a lot of time here as I could simply use `FROM ruby:2-alpine` instead and the image was rebuilt. Installing this locally it would have been a headache to manage ruby versions. I had this issue before when Python 3 was released and ended up with multiple conflicting versions of packages on my machine (I just use *venv* now). 

The following  `RUN` line is likely to be the most contentious. I had considered replacing the line:

``` dockerfile
RUN apk add gcc g++ make && gem install bundler jekyll && apk del gcc g++ make
```
with:

``` dockerfile
RUN apk add gcc g++ make 
RUN gem install bundler jekyll && apk del gcc g++ make 
```

If you are not familiar with Docker or how Dockerfiles are built you may be questioning my sanity at this point. Yes this does run the same commands but there are differences to the image that is produced. The second example may be preferable if you work with ruby a lot. This will cache a version of ruby:2-alpine with gcc, g++ and make installed. If you building a container that uses this in future it can use the cached version. It's also worth noting that the `apk del gcc g++ make` in the second example will not reduce image size which makes sense if you think about docker images as a stack of layers. I decided to go with the first image as it was approximately 200MB smaller and the frequency of image downloads would be higher than the frequency of me installing new ruby gem (I expect both cases will be rare occurrences).

## Deploying the Container Locally

If you would like to run this container you can pull it from dockerhub by typing the following into your terminal (you will need Docker installed):

``` sh
docker pull svmeehan/jekyll
```

You can then run the container by executing the following command (You can also just run this line and the image should be pulled automatically):

``` sh
docker container run --rm -d -p 4000:4000 --name jekyll svmeehan/jekyll
```
You may want to include a volume so you can change files on the fly and we will discuss this in the next section. You can then access your new blog by navigating to http://localhost:4000 in a browser.

## Getting this to work with Github Pages

In order to push our blog to github we need to take a number of steps. First we need to create a new repository on Github and it should be named *yourusername*.github.io. This name is required for the page to appear on Github pages. Otherwise it will just be a normal repository.

Next we need some local files to push. In the last section we mentioned volumes in docker and we are going to use one of these. For consistency on the next few commands I will call the local folder my-blog but it makes more sense in practice to call it the same name as your repo as this is the name you will get if you clone it. First thing we do is create the folder for our local blog and then initialize it as a git repository.

``` sh
mkdir my-blog
cd my-blog
git init
```
Next we need to get the files that compose the basic blog from the container by executing the following command in our local blog folder. In the below we are copying from the /my-blog folder inside the container to our local file.

``` sh
docker container cp jekyll:/my-blog/. .
```

You should now see _posts and _site directories in your local my-blog directory. We need to link our local file to the git repo which we can do using the following command:

```
git remote add origin *yourusername*.github.io
```

We are now ready to add, commit and push but first let's open this local blog with the container. This command should be run one level up  just outside the local my-blog folder.

``` sh
docker container run --rm -d -p 4000:4000 -v $(pwd)/my-blog:/my-blog --name jekyll svmeehan/jekyll
```

Now if we go to our posts folder and begin to edit the basic post included we should see the change reflected @ http://localhost:4000.

Finally we add commit and push our changes which is pretty standard. We need to be in the local my-blog folder again.

``` sh
git add . #add all files
git commit -m "First commit, placeholder blog" #commit with a message
git push origin master #this will push our files
```

Github will then build this for you and will even send you an email when it fails. It may take about a minute a push to be reflected in you webpage.

You can now visit your site at: http://*yourusername*.github.io.

Make some changes to your local files, make sure they good by accessing the site on the container and finally push your changes. Enjoy your new blog!