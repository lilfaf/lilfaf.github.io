---
layout: post
title: "Play Vimeo Videos on iOS With Ease"
date: 2013-10-08 13:46
comments: true
categories: [CocoaPods, iOS, Objective-C]
---

### Context

A year ago two friends started an unusual participative trip around the world. They came up with a crowd funded project called [You Make My Trip](http://www.youmakemytrip.com). Nearly every month they let users vote for a challenge based on the customs of the country they are visiting at the current time. Once the challenge has been completed they report their adventure on a mini documentary video. As a friend and supporter, I started building an iphone application but problem was most of their videos were hosted on Vimeo.

So far the only way to play Vimeo video on an iphone application was to embed their [Universal Player](http://developer.vimeo.com/player/embedding) into a UIWebView widget. Unfortunately, using this technique I often had an error message saying that the video couldn’t be played on mobile devices.

Therefore I started working on another solution to play all videos without a glitch, called it YTVimeoExtractor.

<!-- more -->

### Introduting YTVimeoExtractor

YTVimeoExtractor helps you fetch mp4 urls which can be use in iOS’s native player. You can even choose between mobile, standard and high definition quality. The coolest point is that it doesn’t use any UIWebView which makes it fast and clean.

YTVimeoExtractor supports iOS 4.0 and above as it is deployed for an ARC environment.

## Install

I recommend installing using [CocoaPods](http://cocoapods.org). Just add to your Podfile

```ruby
pod 'YTVimeoExtractor'
```

then run `pod install`.

Alternatively you can just copy the [YTVimeoExtractor](https://github.com/lilfaf/YTVimeoExtractor/tree/master) folder to your project.

```objc
#import "YTVimeoExtractor.h"
```

## Usage

You can either use the block based method passing it the video url and the desired quality

```objc
[YTVimeoExtractor fetchVideoURLFromURL:@"http://vimeo.com/58600663"
                               quality:YTVimeoVideoQualityMedium
                               success:^(NSURL *videoURL) {
    NSLog(@"Video URL: %@", [videoURL absoluteString]);
}
failure:^(NSError *error) {
    // handle error
}];
```

or you can use delegation. Just create an YTVimeoExtractor instance.

```objc
self.extractor = [[YTVimeoExtractor alloc] initWithURL:@"http://vimeo.com/58600663"
                                               quality:YTVimeoVideoQualityHigh];
self.extractor.delegate = self;
[self.extractor start];
```

then implement YTVimeoExtractor callback methods in your ViewController.

```objc
- (void)vimeoExtractor:(YTVimeoExtractor *)extractor didSuccessfullyExtractVimeoURL:(NSURL *)videoURL
{
    // launch movie player
    self.playerViewController = [[MPMoviePlayerViewController alloc] initWithContentURL:videoURL];
    [self.playerViewController.moviePlayer prepareToPlay];
    [self presentViewController:self.playerViewController animated:YES completion:nil];
}

- (void)vimeoExtractor:(YTVimeoExtractor *)extractor failedExtractingVimeoURLWithError:(NSError *)error;
{
    // handle error
}
```

Check out the [sample](https://github.com/lilfaf/YTVimeoExtractor/tree/master/Sample) application for more details.
