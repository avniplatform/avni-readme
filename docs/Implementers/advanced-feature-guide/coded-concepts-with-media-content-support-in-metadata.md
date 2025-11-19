---
title: Coded Concepts with media content support in metadata
deprecated: false
hidden: false
metadata:
  robots: index
---
## Overview

Avni supports adding **media content** (videos and images) to coded concepts through metadata. This feature allows concepts to reference media files that are synced to field devices and can be displayed within forms.

## Feature Description

Media content support to concept metadata enables:

* Adding video and image references via "App Designer" 
* Automatic syncing of media files to field devices
* Inline display of media content within forms
* Offline access of the synced media files

## Configuration

### Adding Media to Concepts

1. Navigate to **Concepts** tab, in Avni admin interface, "App Designer" section
2. Create a new concept or edit an existing one
3. In the concept editor, locate the media upload sections:
   * **Image (max 150 KB)**: Upload JPG or PNG image files
   * **Video (max 10 MB)**: Upload MP4 video files
4. Upload the desired media file(s)
5. Save the concept

Media files are automatically synced to field devices during the regular sync process and become available offline.

> <Image align="center" alt="Concept editor showing media upload fields for image and video" border={false} src="https://files.readme.io/f73a540a8823356f68a3f751f8a7a56dbbb70d832816e815a3185a0746c9f293-ConceptsWithMediaEdit.png" />

### Using Media-Enabled Concepts in Forms

Media added to a concept can be used in forms as follows:

* **Answer options**: Display images or videos alongside answer choices to guide user selection
* **Question explanations**: Show supplementary media to clarify what information is being collected

> <Image align="center" alt="App Designer showing media-enabled concepts integrated into form questions and answer options" border={false} src="https://files.readme.io/dd576e833fcaa2057680c629419ec653f8e629cd9a28a5dea6f08579062d5000-ConceptsWithMediaView.png" />

### Supported Media Types

* **Videos**: MP4 format - max 10 MB
* **Images**: JPG, PNG formats - max 150 KB

### Technical Considerations

* Media playback depends on device capabilities
* Test on target devices before deployment
* Storage constraints may limit media file quantities on field devices
* Network connectivity required for initial sync
* Media files are cached locally after sync for offline access

## Mobile application capability

### Sync and Storage

* Media files are downloaded during standard Avni sync process
* Files are cached locally for offline access
* Storage management follows Avni's existing file handling patterns

### Field Worker Experience

When a form contains media-enabled concepts, field workers see:

1. **Media Icons**: Small media icons (video or image) appear next to the question or answer options
2. **Accessing Media**: Tap the media icon to view the content
3. **Video Playback**: Videos open in the device's default video player or play inline
4. **Image Display**: Images open in the device's image viewer or display inline
5. **Offline Access**: All synced media is available even without internet connection
6. **Guided Answers**: Visual media helps field workers understand and select the correct answer options

> <Image align="center" alt="Mobile app view showing media icons next to answer options (video and image icons visible)" border={false} src="https://files.readme.io/9c17f25cadff6372d7fd31839c47342888a8087696c1c221a93938de532a8c78-FormEdit.png" />

<br />
