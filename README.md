spriteclip.js
====

Frame-by-frame animations in JS made easy by using an interface similar to AS3's MovieClip.


Overview:
-------------
spriteclip.js is a jQuery plugin that makes it easy to make snazzy frame-by-frame animations by emulating the functionality of the MovieClip class in ActionScript 3.0.

It works by animating the background-image property of a dom node where the "frames" are spaced out equally. Eg. if the sprite is laid out horizontally and each frame is 20px wide, frame 1 will be at background-position-x: 0 and frame 2 will be at background-position-x: -20px

Once instantiated, you get access to an instance with familiar methods, properties and events like:

	clip.play();
	clip.stop();
	clip.gotoAndPlay();
	clip.gotoAndStop();
	clip.nextFrame();
	clip.prevFrame();

	clip.currentFrame
	clip.totalFrames
	clip.frameRate

	SpriteClipEvent.ENTER_FRAME

.. Plus extra sugar like:
	
	clip.playtoAndStop();
	clip.gotoAndRewind();
	clip.rewind();
	clip.rewindtoAndStop();

	clip.isPlaying

	SpriteClipEvent.PLAYING
	SpriteClipEvent.STOPPED


Background:
-------------
The project was inspired by the neat rollovers on http://beta.rallyinteractive.com, that prompted me to think that the reason that you almost never see these awesome yet relatively simple animations that were so common in the flash days must be that the tools for creating them in Javascript were none existant.

I thought that a MovieClip class in Javascript would be a great base as that is the very first class you learn to work with in Flash and hence the learning curve would be flat for as many developers as possible. That is also why I have aimed to make an interface that is as consistent with the flash interface as possible and only added a few commonly used methods and properties like rewind() and isPlaying. Further additions can then easily be made in an class that inherits.


Instantiation:
-----------
The Spriteclip object is exposed as a jQuery plugin but all logic is kept in a classic object. This means that you can choose to instantiate either via the plugin or via the SpriteClip constructor. 

If you instantiate via the plugin, the instance is stored in the jQuery elements data-spriteClip attribute so it can easily be retrieved through the DOM.

	var options = {
	        totalFrames: 6, 		//Required - the number of frames in the sprite
	        frameRate: 30, 			//Optional - the framerate the clip should play at
	        frameWidth: 35, 		//Optional - width of each frame - defaults to elements width + padding - border
	        frameHeight: 100, 		//Optional - height of each frame - defaults to elements height + padding - border
	        layout: "horizontal", 	//Optional - the layout of the sprite
	        stops: [] 				//Optional - an array of frames to stop at
	     },
	     clip;
	 
	//Call the plugin
	$("#domElement").spriteClip(options);
	clip = $("#domElement").data("spriteClip");
	 
	//Equivalent to:
	clip = new SpriteClip(document.getElementById("bernard"), options);


Events:
-----------
Each instance HAS an eventdispatcher attached instead of BEING an eventdispatcher. This means that you subscribe to the instance's events through it's $dispatcher property. The $dispatcher is nothing but a dummy jQuery element that isn't attached to the DOM, which we use because jQuery has a great event system built in with methods like .on(), .off(), .trigger() and .triggerHandler() 
	
	//Events that can be subscribed to are ENTER_FRAME, STOPPED or PLAYING
	clip.$dispatcher.on(SpriteClipEvent.ENTER_FRAME, function() {
		//Stuff that should be done every time a new frame is shown
	}):

For a more detailed demo of the API, check out <a href="moredots.dk/projects/spriteclip/demos/apidemo.html" target="_blank">moredots.dk/projects/spriteclip/demos/apidemo.html</a>



Performance:
-----------
- Only 1 timeout pr. frameRate:
When a clip is played/stopped, it is registered/unregistered in a central timeout manager, which is responsible for updating all playing clips. This means that if we have 5 clips playing at 20 fps, and 5 at 30 fps, only 2 timeouts are running. One every 20th of a second and one every 30th.