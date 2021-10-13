---
layout: post
title: simonknott.de
categories:
    - Web Development
    - Projects
description: Working with Jekyll as a static CMS
image: https://jekyllrb.com/img/logo-2x.png
lang: en
comments: true
---
I played with the thought of getting my own web page for quite a while now. I then lately found out that all attractive domains for my family are already claimed, and so I decided to buy **MY** domain.  

##### Finding a webhoster
So I went out and searched for a good webhoster with basic features, because I didn't want to pay that much.  
And here I am: this site is hosted by Hetzner, and it's their cheapest packet at around 20€ per year. For that, I get static website hosting without any serverside stuff except for PHP.  
That may sound limiting, but for my needs this is totally fine. With the right optimizations made, static pages allow for blazing fast loading times, which helps a great deal in search engine optimization.
According to Google's [Test My Site](https://testmysite.withgoogle.com), this page has loading times under 2 seconds, which makes it double the speed of the top branche competitors.  
<!--more-->

##### Goal for the site
Once I had bought the site, I thought about what I wanted to do with it. The first step was to bring up a "Site Under Construction"-Page, so it was clear that there's something coming. For that, I used the beautiful [Aerial](https://html5up.net/aerial) template by [HTML5UP](https://html5up.net), who make great templates. If you don't want the hassle of designing your own page, definitely check them out!  
I quickly realized that I also wanted to host my "Lernblog" on this site, since I didn't really like dealing with Wordpress in the first place. By moving it, I also had some contents readily made so the page didn't look as blank as it would without. By this, my criteria for hosting this blog resulted in a service that creates static sites without the hassle of webinterfaces which preferably could also import Wordpress exports.

So I did some searching around and quickly found Jekyll to be the software I want to use! :D

##### Jekyll
[Jekyll](https://jekyllrb.com/) is a static site generator. That means that it takes in your posts as text and then "renders" out the appropriate HTML pages. This enables it to have both the advantages of a CMS (not having to deal with HTML) and of static webpages (speed).
I use the [Leonids](https://github.com/renyuanz/leonids) theme by renyuanz, which is a stunning theme with great expendability. And because there wasn't a resume page in the theme, I made my own!

This is where jekyll really shines: Expanding the page is no problem. The directory structure consists of a folder named `/_includes` where you can place HTML files that you then can include into your pages. This is why my `resume.html` looks like this: 
```html
---
layout: resume
title: Resume
permalink: /resume/
---
```
The triple dashes tell Jekyll that this is the config section of the page. We can specify the layout that it extends from, a title for it and a permalink. That's all!

When whe look into the referenced file `/_layouts/resume.html` we see the following:

```html
---
layout: default
---
<div class="row">
    <div class="col l7">
        <div class="row">
            <div class="col">
                { % include sections/about.html % }        
            </div>
        </div>
        <div class="row">
            <div class="col">
                { % include sections/projects.html % }
            </div>
        </div>
    </div>
    <div class="col l4 offset-l1">
        <div class="row">
            { % include sections/skills.html % }     
        </div>
        <div class="row">
            { % include sections/career.html % }    
        </div>
        <div class="row">
            { % include sections/education.html % }
        </div>
    </div>
</div>
```
> Don't include the whitespaces between the curly braces and the percentage sign. They're just for disabling rendering so Jekyll doesn't include them in this post :)  

At the top, we have our triple dashes region. In there, the layout to extend from gets specified. The default layout includes things like the sidebar, JavaScript or the footer.
Then the page is split up into some rows with the help of Bootstrap rows and columns.
The magic happens at the `{ % include foo % }` part:  
At render time, Jekyll simply replaces these expressions with the html code it finds in the include file specified. Have a look at the source code of the resume page, you can see the bootstrap skeleton as well as the normal html code from the includes.

In the include files themselves, there's still some magic:
The Bio and Education regions use the theme's beautiful timeline view, and the rest is simple HTML - but it dynamically gets the data to display from special *data* files in `/_data`. This is another feature of jekyll: Data and displaying logic can be strictly separated, much like in a CMS. Let's take a look at the `career.html` include file:

```html
<div class="navy-line"></div>
<h1><span data-i18n="skills.my_skills">Education</span></h1>

<div class="archive">
  <div class="timeline" id="timeline">
    { % for career in site.data.index.education % }
          <div class="archive-title bio"><div class="archive-year"><strong>{ { career.date } }</strong> <a href="{{ career.link }}" target="_blank">{ { career.name } }</a></div></div>
    { % endfor % }
  </div>
</div>
```
> Don't include the whitespaces between the curly braces. They're just for disabling rendering so Jekyll doesn't include them in this post :)  

In the top two lines, there's a simple header.
Then, inside two other divs, you can see the `for` tag - this acts as a regular for-each loop, and gets closed by the `endfor` tag. In between, there's the HTML code that get's rendered once for every iteration of the loop. In between the double curly braces, there's the variable that gets inserted.
This loop accesses the file `_data/index/education`. Inside it, there's a rather simple database of every career that I want to be displayed in the section:

```yml
- name: Fraunhofer FIT
  link: https://www.fit.fraunhofer.de/
  date: July 2017
  job: Programming Internship

- name: MobileBeat
  link: http://www.mobilebeat.com/
  date: May 2017 - <i class="far fa-clock" aria-hidden="true" title="Until Now"></i>
  job: Web Assistant

- name: EMA
  link: http://ema-bonn.de/
  date: 2016 - 2017
  job: Student Representative
```

This should be self-explanatory. For every career entry, I have specified some properties so the include can access these.

##### Conclusion
While static webhosting may sound limiting at first, especially when you want to host a blog and can't just install Wordpress onto it, it really is a convenient experience - with the right tools. By using Jekyll, I can write my Blog posts in MarkDown and I am not limited to only being able to write them with internet Access, and it is a great experience just being able to make your drafts via Git branches. I highly recommend trying Jekyll, especially since it's free and open-source.

<i class="fa fa-fw fa-file-text"></i> [Jekyll Homepage](https://jekyllrb.com)