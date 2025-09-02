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

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/697f439-image.png",
        null,
        "Media Viewer app"
      ],
      "align": "center",
      "border": true,
      "caption": "Media Viewer app"
    }
  ]
}
[/block]


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

- **Thumbnails** are shown in the search/filter results. Thumbnails are generated automatically by Avni.
- When you click an image the preview of image is shown, this is the **original image** shown in a fixed size.
- When you download the image the **original image** is downloaded.
- When you click on the name the image **original image** is shown in the new tab.

### Display of non-open formats of images

`heif` and `heic` are two image format (known to Avni team) that cannot be displayed in the browser and cannot be processed by standard libraries to generate thumbnails. These image formats are known to come from some Samsung devices.

Due to this, the thumbnails are not visible in the media viewer web app. But you can only download the full size images for the same.

Currently the users can upload standard images by turning of this feature in Samsung. There are two settings that need to be changed as described in this short video.

[block:embed]
{
  "html": "<iframe class=\"embedly-embed\" src=\"//cdn.embedly.com/widgets/media.html?src=https%3A%2F%2Fwww.youtube.com%2Fembed%2F7MLuT-dVuf0%3Ffeature%3Doembed&display_name=YouTube&url=https%3A%2F%2Fwww.youtube.com%2Fwatch%3Fv%3D7MLuT-dVuf0&image=https%3A%2F%2Fi.ytimg.com%2Fvi%2F7MLuT-dVuf0%2Fhqdefault.jpg&key=7788cb384c9f4d5dbbdbeffd9fe4b92f&type=text%2Fhtml&schema=youtube\" width=\"854\" height=\"480\" scrolling=\"no\" title=\"YouTube embed\" frameborder=\"0\" allow=\"autoplay; fullscreen; encrypted-media; picture-in-picture;\" allowfullscreen=\"true\"></iframe>",
  "url": "https://www.youtube.com/embed/7MLuT-dVuf0?si=B3D3GwQK8_08nXX0",
  "title": "How to Fix Android Phone Shooting Picture in HEIC/HEIF Format | Samsung Mobile",
  "favicon": "https://www.youtube.com/favicon.ico",
  "image": "https://i.ytimg.com/vi/7MLuT-dVuf0/hqdefault.jpg",
  "provider": "youtube.com",
  "href": "https://www.youtube.com/embed/7MLuT-dVuf0?si=B3D3GwQK8_08nXX0",
  "typeOfEmbed": "youtube"
}
[/block]