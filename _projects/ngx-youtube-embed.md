---
layout: md
title: ngx-youtube-embed
repo: chrisweaver1/ngx-youtube-embed
description: Angular component for embeding Youtube videos into Angular projects.
tags: ['angular', 'html', 'typescript', 'npm']
---

{% include project-headers.html %}

[![npm version](https://badge.fury.io/js/ngx-youtube-embed.svg)](https://badge.fury.io/js/ngx-youtube-embed)
[![npm downloads a month](https://img.shields.io/npm/dm/ngx-youtube-embed.svg)](https://img.shields.io/npm/dm/ngx-youtube-embed.svg)
[![npm downloads a week](https://img.shields.io/npm/dt/ngx-youtube-embed.svg)](https://img.shields.io/npm/dt/ngx-youtube-embed.svg)

An Angular component that allows the embedding of youtube videos into an angular webpage. Options and event hooks can be passed through to the component so the user can fully utilize [Youtube's embed/iframe api](https://developers.google.com/youtube/iframe_api_reference). 

```html
<youtube-embed [videoId]="id" width="1280" height="720" (ready)="savePlayer($event)"
        (change)="onStateChange($event)" [protocol]="'https'" 
        [playerVars]="{ controls: 1, showinfo: 0, rel: 0, autoplay: 1, modestbranding: 0 }">
</youtube-embed>
```

This was a small project for something we were doing at work as nothing really met our requirements at the time. I decided to put this togther, using parts from other projects and packaged it up for internal use, later making it public. 

{% include tags.md %}