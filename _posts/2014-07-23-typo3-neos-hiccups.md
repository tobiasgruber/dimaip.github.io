---
layout: post
title: My hiccups with TYPO3 Neos
date: 2014-07-23 12:13:32
tags: neos typoscript
comments: true
published: true
---

## Day 1

### 1. Forgot to include TypoScript

I assumed that TYPO3 Neos includes TypoScript automatically for every node type.  
Of course when I tried to access some property from template, which I thought I had defined, I got the following  error:

```
No "page/body/content/main/default/element/itemRenderer/default/element/column0" TypoScript object found. Please make sure to define one in your TypoScript configuration. (20140723102105f65989)
```

TODO: lookup what this does:

```
TYPO3:
  Neos:
    typoScript:
      autoInclude:
```

It was my fault, but still I wish the docs would mention it somehow.

**Time wasted**: 2 hours.

**The solution**: include the relevant typoscript file from your root typoscript file!

```
include: NodeTypes/YourElement.ts2
```


### 2. Property names must not contain dashes!

Here's my second hiccup: when trying to implement Foundation Grid, I names one of the properties `large-offset`. Of course it didn't work. 

**Time wasted**: 15 min.

**Solution**: use lowerCamelCase when naming NodeType properties.

---------

## Day 2

### 3. Flush caches in Production

After switching to production context, Neos wasn't able to find my custom Node Types. I smelled cache issues so I was able to quicly google this up:

`FLOW_CONTEXT=Production ./flow flow:cache:flush --force`

**Time wasted**: 15 min.


### 4. Stuck in edit preview mode

[https://forge.typo3.org/issues/54336](https://forge.typo3.org/issues/54336)

**Solution**: Add `print < page` in your root.ts2

**Time wasted**: 10 min.

-----

## Day 3

### 5. Access properties of a node from template

I've assigned a some node as a category, and tried to display it in fluid template like this: `<neos:link.node node="{category}">{category.title}</neos:link.node>`. Didn't work!

**Solution**: Use `{category.properties.title}` instead: `<neos:link.node node="{category}">{category.properties.title}</neos:link.node>`

**Time wasted**: 10 min.

## A few weeks later

I've been busy migrating content for a while, and all went well so far.

Once in a while I needed to clear all site data and start all over again. Here's how to do it:

Run the following SQL to kill all of your media resources:

```
SET FOREIGN_KEY_CHECKS=0;
truncate typo3_flow_resource_resource;
truncate typo3_flow_resource_resourcepointer;
truncate typo3_media_domain_model_asset;
truncate typo3_media_domain_model_image;
SET FOREIGN_KEY_CHECKS=1;
```

Notice that you have to disable key checks before truncating, otherwise it won't work.

Delete files itself: `rm -f Data/Persistent/Resources/*` and `rm -f Web/_Resources/Persistent/*`
 
`./flow site:prune --confirmation TRUE` -- clean website data and import your site data after: `./flow site:import --yoursitehere`

