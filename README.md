# Multimedia ePub Internship documentation-cum-presentation
# by Adam Obeng

## Introduction

This document aims to document how to produce EPUB eBook which make use of various multimedia features, summarizing the work carried out by [Adam Obeng](http://adamobeng.com) at OUCS during July and August 2011.

The EPUB format is open and well documented elsewhere, so this document won't attempt to provide an overview in general. 

### Example files

Example files should be available in the OUCS Infodev Projects Repo (infodev/projects/adam).


## Available technologies

The stuff you can do in an EPUB3 eBook is quite similar to what you can do on a web site, but there are both restrictions and additions.
Notably:
	* Watch out for any user-interface stuff that needs to work on touch devices (e.g. :hover pseudo-attribute won't work)
	* Javascript support may be iffy. Use a library like jQuery to do cool stuff in a way that probably won't break
	* EPUB-specific features include:
		- Media Overlays
		- Fixed-layout eBooks (although this is Apple-specific and not part of the standard. Other reader software makers may choose to implement this in future.)

### Images and facsimilies

These are relatively straightforward: if `teitoepub --profile=otapages` detects a `fasc` attribute on a `<pb>` element, then it creates a page for the image pointed to by the `fasc` attribute.

In order to give the best control over layout (and to ensure that the image ends up next to the correct corresponding text), the EPUB created is [fixed-format](#fixed-format). Following Liz Castro's reccomendations, the page dimensions are 1200 by 1700 pixels. This size fits neatly on the screen, and images that are intended to fill the page should be of this size. Also watch out that in the CSS, no borders or margins are being applied to the `img` or `body` elements.

Non-fascimile images should not be much trouble.

### Audio and video embedding

This is accomplished by use of the HTML5 `<audio>` and `<video>` elements (only the latter has been tested). As with HTML5, the supported formats are left to the renderer (but Apple's iBookstore Asset Guide will tell you which formats they like).

Importantly, you must specify the `controls="controls"` attribute, and not `autoplay="autoplay"` unless you have a very good reason. You should probably also specify a poster art with your image.

It should also be possible to use Javascript to play back the text, but this hasn't been tested. There is a jQuery plugin that does subtitle rendering over HTML5 `<video>` elements, which might be interesting if you already are using a transcript for making a Media Overlay.

At the time of writing, docxtotei can extract video from docx documents, and the author is investigating whether the `mimeType` attribute of a TEI `<figure>` could be used to determine whether to generate `<audio>`, `<video>`, or `<img>` tags in the eBook.

Also note that Apple support "ambient music" as part of a Media Overlay.

### Fixed Layout EPBUBs

	As noted above, this an Apple-specific feature at the time of writing. Full details on how to implement it can be obtained from the iBookstore Asset Guide.

	A tested example is available in the `examples` directory.


## Extensions to TEI XML
For the purposes of the projects, it was (amazingly) found that TEI was somewhat lacking. Therefore, the spec was ignored where unavoidable>

`<when>` elements now take `@from` and `@to` attributes, which are used by `teitoepub` to generate the EPUB's SMIL file.
Within a `<figure>`, if a `<graphic>` is specified with the `@n='thumbnail'`  attribute, and another `<graphic>` in the same `<figure>` has the attribute `@mimetype=video/*` then it assumed to be the thumbnail (poster) for the video when output is XHTML (including EPUB).

## Programs

### teitoepub, docxtotei, etc.
These are based on the TEI Stylesheets, and also are used for oXgarage. The repository is at [Sourceforge](http://tei.svn.sourceforge.net/svnroot/tei).
To generate fixed-layout epubs, `teitoepub` should be called with the option `--fileperpage`. This should also create a media overlay for an appropriate TEI XML file. 

### Jquery
No AJAX, load, get
No adding new elements to DOM

### lumberjack

**lumberjack** is used to link an audio recording to an already existing transcription (for example, linking a play's performance to its text). There are other tools available for making transcriptions where they do not already exist (such as [Praat](http://www.fon.hum.uva.nl/praat/) and [Transcriber](http://trans.sourceforge.net/en/presentation.php).

Here's how it works: you point it at a TEI XML file and an audio file, and it displays onscreen one transcribeable element of the file at a time (typically a line). While the audio is playing, you hit the space bar to indicate that the displayed section has finished, and **lumberjack** moves on to the next section. If there's an inconsistency between the audio and the text, or you make a mistake, mash the keyboard wildly (or just press any key other than space, p, or q).

More documentation on **lumberjack** is available in its 'docs' folder.

### overflow.py
Use overflow.py to check whether the XHTML pages you're generating will fit on screen. This only applied to Fixed Layout EPUBs. This script is quite crude, as it spits .png files out, which it doesn't remove. It's a good idea to run it pointed at files in a directory which is temporary. **overflow.py** depends on CutyCapt.


## Useful Resources

Liz Castro's reccomendations
A description of my talk at the Digital.Humanities@Oxford Summer School 2011 is available [on my website](http://adamobeng.com/dhox). This also includes the slides and the demonstration EPUB that includes most of the features discussed in this document.
Apple's iBookstore Asset Guide details what Apple expects of iBooks that go into the iBookstore. Note that the reccomendations need not be met for files to work in iBooks if transferred locally (e.g. .wav file audio will work as well as .m4a). In general, this imposes additional restrictions on file formats as well as detailing Apple-specific features (like fixed-format).
 
