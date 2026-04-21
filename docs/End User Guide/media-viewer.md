---
title: Media Viewer
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
If you collect media (images, video, files) as part of your workflow then Avni Media Viewer will help your users to browse through, search and bulk download such media files. Media Viewer is available as an web app on the home page.

<Image align="center" alt="Media Viewer app" border={true} caption="Media Viewer app" src="https://files.readme.io/697f439-image.png" />

Media can be filtered by

1. Addresses
2. Subject Types
3. Programs
4. Encounter Types
5. Concepts (numeric, text and coded datatypes are supported)

Media can be downloaded as a zip file with format described [here](https://gist.github.com/vinayvenu/eabbb7c376e32f5bf665c7a0b595f524)

### Important to note

1. Media Viewer can be used only if an organisation has analytics (ETL) enabled
2. The thumbnails are generated seperately as part of AWS Lambda jobs.

### Alternative methods to access media

Other than the Media Viewer app, media can be accessed using the following mechanisms

1. Going to the specific form where the media was collected (either in the web based Data Entry Application or the Android app)
2. Using a [report](https://avni.readme.io/docs/accessing-media-in-reports) to list out a specific media files

### Thumbnails and original image

* **Thumbnails** are shown in the search/filter results. Thumbnails are generated automatically by Avni.
* When you click an image the preview of image is shown, this is the **original image** shown in a fixed size.
* When you download the image the **original image** is downloaded.
* When you click on the name the image **original image** is shown in the new tab.

### Display of non-open formats of images

`heif` and `heic` are two image format (known to Avni team) that cannot be displayed in the browser and cannot be processed by standard libraries to generate thumbnails. These image formats are known to come from some Samsung devices.

Before v15.2: Thumbnails for these images were not visible in the media viewer web app, and users could only download the full-size images. To work around this, users could upload standard images by turning off this feature in Samsung — there are two settings that need to be changed, as described in this short video.

From v15.2 onwards: All heic/heif images are automatically converted to jpeg/jpg so that they can be displayed and thumbnailed normally in the media viewer. Note that metadata such as location information is lost during this conversion process and we might fix this in future.

<Embed url="https://www.youtube.com/embed/7MLuT-dVuf0?si=B3D3GwQK8_08nXX0" href="https://www.youtube.com/embed/7MLuT-dVuf0?si=B3D3GwQK8_08nXX0" typeOfEmbed="youtube" html="%3Ciframe%20class%3D%22embedly-embed%22%20src%3D%22%2F%2Fcdn.embedly.com%2Fwidgets%2Fmedia.html%3Fsrc%3Dhttps%253A%252F%252Fwww.youtube.com%252Fembed%252F7MLuT-dVuf0%253Ffeature%253Doembed%26display_name%3DYouTube%26url%3Dhttps%253A%252F%252Fwww.youtube.com%252Fwatch%253Fv%253D7MLuT-dVuf0%26image%3Dhttps%253A%252F%252Fi.ytimg.com%252Fvi%252F7MLuT-dVuf0%252Fhqdefault.jpg%26key%3D7788cb384c9f4d5dbbdbeffd9fe4b92f%26type%3Dtext%252Fhtml%26schema%3Dyoutube%22%20width%3D%22854%22%20height%3D%22480%22%20scrolling%3D%22no%22%20title%3D%22YouTube%20embed%22%20frameborder%3D%220%22%20allow%3D%22autoplay%3B%20fullscreen%3B%20encrypted-media%3B%20picture-in-picture%3B%22%20allowfullscreen%3D%22true%22%3E%3C%2Fiframe%3E" />
