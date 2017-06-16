---
title: Personalized video thumbnails
excerpt: Course project of CPSC 554M
header:
  image:
  teaser: /assets/images/portfolio/personalized_video_thumbnail_th.jpg
gallery:
  - url: /assets/images/portfolio/cpsc554m/youtube.jpg
    image_path: /assets/images/portfolio/cpsc554m/youtube.jpg
    alt: "Youtube thumbnail interface"
gallery1:
  - url: /assets/images/portfolio/cpsc554m/lo_fi/player1.JPG
    image_path: /assets/images/portfolio/cpsc554m/lo_fi/player1.JPG
    alt: "player 1"
  - url: /assets/images/portfolio/cpsc554m/lo_fi/player2.JPG
    image_path: /assets/images/portfolio/cpsc554m/lo_fi/player2.JPG
    alt: "player 2"
  - url: /assets/images/portfolio/cpsc554m/lo_fi/filmstrips.JPG
    image_path: /assets/images/portfolio/cpsc554m/lo_fi/filmstrips.JPG
    alt: "filmstrip"
  - url: /assets/images/portfolio/cpsc554m/lo_fi/thumbnail1.JPG
    image_path: /assets/images/portfolio/cpsc554m/lo_fi/thumbnail1.JPG
    alt: "thumbnail 1"
  - url: /assets/images/portfolio/cpsc554m/lo_fi/thumbnail2.JPG
    image_path: /assets/images/portfolio/cpsc554m/lo_fi/thumbnail2.JPG
    alt: "thumbnail 2"
gallery2:
  - url: /assets/images/portfolio/cpsc554m/me_fi/player_prototype.png
    image_path: /assets/images/portfolio/cpsc554m/me_fi/player_prototype.png
    alt: "player prototype"
  - url: /assets/images/portfolio/cpsc554m/me_fi/medium-fi_prototype.png
    image_path: /assets/images/portfolio/cpsc554m/me_fi/medium-fi_prototype.png
    alt: "medium-fi prototype of player and filmstrip"
gallery3:
  - url: /assets/images/portfolio/cpsc554m/hi_fi/player.jpg
    image_path: /assets/images/portfolio/cpsc554m/hi_fi/player.jpg
    alt: "player prototype"
  - url: /assets/images/portfolio/cpsc554m/hi_fi/mixed.jpg
    image_path: /assets/images/portfolio/cpsc554m/hi_fi/mixed.jpg
    alt: "mixed thumbnail"
  - url: /assets/images/portfolio/cpsc554m/hi_fi/automatic.jpg
    image_path: /assets/images/portfolio/cpsc554m/hi_fi/automatic.jpg
    alt: "automatic thumbnail"
  - url: /assets/images/portfolio/cpsc554m/hi_fi/videoselection_mixed.jpg
    image_path: /assets/images/portfolio/cpsc554m/hi_fi/videoselection_mixed.jpg
    alt: "video selection interface"
---

This is the course project of CPSC 554M, which is done with the collaboration with Matthew Fong and Xueqin Zhang.

## Goal

As more and more videos are being consumed over the web, organizing and searching for videos becomes more difficult. Videos in at-a-glance search spaces are generally represented by a title, a description, and static thumbnail to show the contents of the video. In the particular case where a user is looking for a video they have seen before, this information may not be enough to trigger a user's memory of the video. We introduce two techniques to represent video in a personalized way that is unique to the user through the analysis of their viewing behaviour, as well as explicit bookmarking for representative thumbnail selection. We performed a formal evaluation on the techniques compared with the traditional YouTube style representation and found users were faster in selecting the video using our methods, and also preferred our method.

### player

The video playing/viewing interface

### filmstrip

A filmstrip consists one or several static/dynamic thumbnails, used to quickly skim through the whole video and jump to the segment of interest.

### thumbnail

A thumbnail is static or dynamic images extracted from videos to represent and summarize the videos.

## Control group

{% include gallery caption="Youtube interface" %}

## Lo-fi prototype

{% include gallery id="gallery1" caption="Lo-fi prototype of player, filmstrip, and thumbnail interface" %}

## Me-fi prototype

{% include gallery id="gallery2" caption="Medium-fi prototype of player, filmstrip, and thumbnail interface" %}

## Hi-fi prototype

{% include gallery id="gallery3" caption="Hi-fi prototype of player, filmstrip, and thumbnail interface, and video selection interface" %}

## Video

<iframe width="560" height="315" src="https://www.youtube.com/embed/AjiB076flu0" frameborder="0" allowfullscreen></iframe>

## Final report

<embed src="/assets/files/cpsc554m_personalized-video-thumbnails.pdf" width="500" height="375" type='application/pdf'>

## Slides

<iframe src="https://docs.google.com/presentation/d/1gf6stA4BZZLoyt0Nk6QpQSoB8qC6mup5HjFcGwzlTYk/embed?start=false&loop=false&delayms=3000" frameborder="0" width="960" height="569" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>

