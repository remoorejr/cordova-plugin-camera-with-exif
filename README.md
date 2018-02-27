cordova-plugin-camera-with-exif
This plugin is an enhanced version of the stock cordova-plugin-camera which provides an API for taking pictures and for choosing images from the system's image library.

It has been modified to extract EXIF and GPS data from all images returned from the either the camera or the image galleries on Android and iOS devices. The image file name and the image metadata (exif,gps) is returned as a JSON string, which needs to be parsed.

The Camera.Destination must be set to FILE_URI and the build must be for Android or iOS to in order for this plugin to work as described above. It will behave as the stock camera plugin in all other cases, returning the image only for all devices.

This plugin is tightly integrated within the Alpha Anywhere PhoneGap App Builder. Alpha Anywhere is a Rapid Mobile Application Development and Deployment platform.

Installation
cordova plugin add cordova-plugin-camera-with-exif
PhoneGap Build plugin installation
Add to config.xml:
<gap:plugin name="cordova-plugin-camera-with-exif" source="npm"/>
navigator.camera.getPicture
Takes a photo using the camera, or retrieves a photo from the device's image gallery. The image is passed to the success callback as a base64-encoded String, or as the URI for the image file. The method itself returns a CameraPopoverHandle object that can be used to reposition the file selection popover.

navigator.camera.getPicture( cameraSuccess, cameraError, cameraOptions );
Description
The camera.getPicture function opens the device's default camera application that allows users to snap pictures. This behavior occurs by default, when Camera.sourceType equals Camera.PictureSourceType.CAMERA. Once the user snaps the photo, the camera application closes and the application is restored.

If Camera.sourceType is Camera.PictureSourceType.PHOTOLIBRARY or Camera.PictureSourceType.SAVEDPHOTOALBUM, then a dialog displays that allows users to select an existing image. The camera.getPicture function returns a CameraPopoverHandle object, which can be used to reposition the image selection dialog, for example, when the device orientation changes.

The return value is sent to the cameraSuccess callback function, in one of the following formats, depending on the specified cameraOptions:

A String containing the base64-encoded photo image.

A String representing the image file location on local storage (default).

You can do whatever you want with the encoded image or URI, for example:

Render the image in an <img> tag, as in the example below

Save the data locally (LocalStorage, Lawnchair, etc.)

Post the data to a remote server

NOTE: Photo resolution on newer devices is quite good. Photos selected from the device's gallery are not downscaled to a lower quality, even if a quality parameter is specified. To avoid common memory problems, set Camera.destinationType to FILE_URI rather than DATA_URL.

Supported Platforms
Amazon Fire OS
Android
BlackBerry 10
Browser
Firefox OS
iOS
Tizen
Windows Phone 7 and 8
Windows 8
Preferences (iOS)
CameraUsesGeolocation (boolean, defaults to true). For capturing JPEGs, set to true to get geolocation data in the EXIF header. This will trigger a request for geolocation permissions if set to true.

 <preference name="CameraUsesGeolocation" value="false" />
Note: this preference is not required with this plugin. It is assumed that the only reason that you are using this plugin is to get geolocation and exif data. If present, this preference has no effect on this plugin.

Amazon Fire OS Quirks
Amazon Fire OS uses intents to launch the camera activity on the device to capture images, and on phones with low memory, the Cordova activity may be killed. In this scenario, the image may not appear when the cordova activity is restored.

Android Quirks
Android uses intents to launch the camera activity on the device to capture images, and on phones with low memory, the Cordova activity may be killed. In this scenario, the image may not appear when the Cordova activity is restored.

Browser Quirks
Can only return photos as base64-encoded image.

Firefox OS Quirks
Camera plugin is currently implemented using Web Activities.

iOS Quirks
With the release of iOS 10 it became mandatory to add a NSCameraUsageDescription and NSPhotoLibraryUsageDescription in the info.plist.

NSCameraUsageDescription describes the reason the app requires access to the camera.
NSPhotoLibraryUsageDescription describes the reason the app requires access to the photo library.
NSLocationWhenInUseUsageDescription describes the reason the app requires access to the devices location.
When the system prompts the user to allow access, this string is displayed as part of the dialog box.

These strings have been hard coded in the plugin.xml file as follows:

NSCameraUsageDescription: This app requires access to the camera to take photos.
NSPhotoLibraryUsageDescription: This app requires access to the photo library to display images.
NSLocationWhenInUseUsageDescription: This app requires access to your location when in use to include location info in photo metadata.
This allows the plugin to work with the PhoneGap CLI as well as PhoneGap Build.

Including a JavaScript alert() in either of the callback functions can cause problems. Wrap the alert within a setTimeout() to allow the iOS image picker or popover to fully close before the alert displays:

setTimeout(function() {
    // do your thing here!
}, 0);
Windows Phone 7 Quirks
Invoking the native camera application while the device is connected via Zune does not work, and triggers an error callback.

Tizen Quirks
Tizen only supports a destinationType of Camera.DestinationType.FILE_URI and a sourceType of Camera.PictureSourceType.PHOTOLIBRARY.

Example
Take a photo and retrieve the image's file location and image metadata (exif, geolocation):

// This iOS/Android only example requires the dialog and the device plugin as well.

navigator.camera.getPicture(onSuccess, onFail, { quality: 50,
    destinationType: Camera.DestinationType.FILE_URI });

function onSuccess(result) {
   // convert JSON string to JSON Object
   var thisResult = JSON.parse(result);

   // convert json_metadata JSON string to JSON Object 
   var metadata = JSON.parse(thisResult.json_metadata);

    var image = document.getElementById('myImage');
    image.src = thisResult.filename

    if (thisResult.json_metadata != "{}") {
        if (device.platform) == 'iOS') {

          // notice the difference in the properties below and the format of the result when you run the app.
          // iOS and Android return the exif and gps differently and I am not converting or accounting for the Lat/Lon reference.
          // This is simply the raw data being returned.

          navigator.notification.alert('Lat: '+metadata.GPS.Latitude+' Lon: '+metadata.GPS.Longitude);
        } else {
           navigator.notification.alert('Lat: '+metadata.gpsLatitude+' Lon: '+metadata.gpsLongitude);
        }

    }
}

function onFail(message) {
    alert('Failed because: ' + message);
}
CameraOptions
Optional parameters to customize the camera settings.

{ quality : 75,
  destinationType : Camera.DestinationType.DATA_URL,
  sourceType : Camera.PictureSourceType.CAMERA,
  allowEdit : true,
  encodingType: Camera.EncodingType.JPEG,
  targetWidth: 100,
  targetHeight: 100,
  popoverOptions: CameraPopoverOptions,
  saveToPhotoAlbum: false };
Options
quality: Quality of the saved image, expressed as a range of 0-100, where 100 is typically full resolution with no loss from file compression. The default is 50. (Number) (Note that information about the camera's resolution is unavailable.)

destinationType: Choose the format of the return value. The default is FILE_URI. Defined in navigator.camera.DestinationType (Number)

  Camera.DestinationType = {
      DATA_URL : 0,      // Return image as base64-encoded string
      FILE_URI : 1,      // Return image file URI
      NATIVE_URI : 2     // Return image native URI (e.g., assets-library:// on iOS or content:// on Android)
  };
sourceType: Set the source of the picture. The default is CAMERA. Defined in navigator.camera.PictureSourceType (Number)

  Camera.PictureSourceType = {
      PHOTOLIBRARY : 0,
      CAMERA : 1,
      SAVEDPHOTOALBUM : 2
  };
allowEdit: Allow simple editing of image before selection. (Boolean)

encodingType: Choose the returned image file's encoding. Default is JPEG. Defined in navigator.camera.EncodingType (Number)

  Camera.EncodingType = {
      JPEG : 0,               // Return JPEG encoded image
      PNG : 1                 // Return PNG encoded image
  };
targetWidth: Width in pixels to scale image. Must be used with targetHeight. Aspect ratio remains constant. (Number)

targetHeight: Height in pixels to scale image. Must be used with targetWidth. Aspect ratio remains constant. (Number)

mediaType: Set the type of media to select from. Only works when PictureSourceType is PHOTOLIBRARY or SAVEDPHOTOALBUM. Defined in nagivator.camera.MediaType (Number)

  Camera.MediaType = {
      PICTURE: 0,    // allow selection of still pictures only. DEFAULT. Will return format specified via DestinationType
      VIDEO: 1,      // allow selection of video only, WILL ALWAYS RETURN FILE_URI
      ALLMEDIA : 2   // allow selection from all media types
  };
correctOrientation: Rotate the image to correct for the orientation of the device during capture. (Boolean)

saveToPhotoAlbum: Save the image to the photo album on the device after capture. (Boolean)

popoverOptions: iOS-only options that specify popover location in iPad. Defined in CameraPopoverOptions.

cameraDirection: Choose the camera to use (front- or back-facing). The default is BACK. Defined in navigator.camera.Direction (Number)

  Camera.Direction = {
      BACK : 0,      // Use the back-facing camera
      FRONT : 1      // Use the front-facing camera
  };
Amazon Fire OS Quirks
Any cameraDirection value results in a back-facing photo.

Ignores the allowEdit parameter.

Camera.PictureSourceType.PHOTOLIBRARY and Camera.PictureSourceType.SAVEDPHOTOALBUM both display the same photo album.

Android Quirks
Any cameraDirection value results in a back-facing photo.

Ignores the allowEdit parameter.

Camera.PictureSourceType.PHOTOLIBRARY and Camera.PictureSourceType.SAVEDPHOTOALBUM both display the same photo album.

BlackBerry 10 Quirks
Ignores the quality parameter.

Ignores the allowEdit parameter.

Camera.MediaType is not supported.

Ignores the correctOrientation parameter.

Ignores the cameraDirection parameter.

Firefox OS Quirks
Ignores the quality parameter.

Camera.DestinationType is ignored and equals 1 (image file URI)

Ignores the allowEdit parameter.

Ignores the PictureSourceType parameter (user chooses it in a dialog window)

Ignores the encodingType

Ignores the targetWidth and targetHeight

Camera.MediaType is not supported.

Ignores the correctOrientation parameter.

Ignores the cameraDirection parameter.

iOS Quirks
Set quality below 50 to avoid memory errors on some devices.

When using destinationType.FILE_URI, photos are saved in the application's temporary directory. The contents of the application's temporary directory is deleted when the application ends.

Tizen Quirks
options not supported

always returns a FILE URI

Windows Phone 7 and 8 Quirks
Ignores the allowEdit parameter.

Ignores the correctOrientation parameter.

Ignores the cameraDirection parameter.

Ignores the saveToPhotoAlbum parameter. IMPORTANT: All images taken with the wp7/8 cordova camera API are always copied to the phone's camera roll. Depending on the user's settings, this could also mean the image is auto-uploaded to their OneDrive. This could potentially mean the image is available to a wider audience than your app intended. If this a blocker for your application, you will need to implement the CameraCaptureTask as documented on msdn : http://msdn.microsoft.com/en-us/library/windowsphone/develop/hh394006.aspx You may also comment or up-vote the related issue in the issue tracker

Ignores the mediaType property of cameraOptions as the Windows Phone SDK does not provide a way to choose videos from PHOTOLIBRARY.

CameraError
onError callback function that provides an error message.

function(message) {
    // Show a helpful message
}
Parameters
message: The message is provided by the device's native code. (String)
cameraSuccess
onSuccess callback function that provides the image data.

function(imageData) {
    // Do something with the image
}
Parameters
imageData: Base64 encoding of the image data, or the image file URI, depending on cameraOptions in effect. (String)
Example
// Show image
//
function cameraCallback(imageData) {
    var image = document.getElementById('myImage');
    image.src = "data:image/jpeg;base64," + imageData;
}
CameraPopoverHandle
A handle to the popover dialog created by navigator.camera.getPicture.

Methods
setPosition: Set the position of the popover.
Supported Platforms
iOS
setPosition
Set the position of the popover.

Parameters:

cameraPopoverOptions: the CameraPopoverOptions that specify the new position
Example
 var cameraPopoverHandle = navigator.camera.getPicture(onSuccess, onFail,
     { destinationType: Camera.DestinationType.FILE_URI,
       sourceType: Camera.PictureSourceType.PHOTOLIBRARY,
       popoverOptions: new CameraPopoverOptions(300, 300, 100, 100, Camera.PopoverArrowDirection.ARROW_ANY)
     });

 // Reposition the popover if the orientation changes.
 window.onorientationchange = function() {
     var cameraPopoverOptions = new CameraPopoverOptions(0, 0, 100, 100, Camera.PopoverArrowDirection.ARROW_ANY);
     cameraPopoverHandle.setPosition(cameraPopoverOptions);
 }
CameraPopoverOptions
iOS-only parameters that specify the anchor element location and arrow direction of the popover when selecting images from an iPad's library or album.

{ x : 0,
  y :  32,
  width : 320,
  height : 480,
  arrowDir : Camera.PopoverArrowDirection.ARROW_ANY
};
CameraPopoverOptions
x: x pixel coordinate of screen element onto which to anchor the popover. (Number)

y: y pixel coordinate of screen element onto which to anchor the popover. (Number)

width: width, in pixels, of the screen element onto which to anchor the popover. (Number)

height: height, in pixels, of the screen element onto which to anchor the popover. (Number)

arrowDir: Direction the arrow on the popover should point. Defined in Camera.PopoverArrowDirection (Number)

      Camera.PopoverArrowDirection = {
          ARROW_UP : 1,        // matches iOS UIPopoverArrowDirection constants
          ARROW_DOWN : 2,
          ARROW_LEFT : 4,
          ARROW_RIGHT : 8,
          ARROW_ANY : 15
      };
Note that the size of the popover may change to adjust to the direction of the arrow and orientation of the screen. Make sure to account for orientation changes when specifying the anchor element location.

navigator.camera.cleanup
Removes intermediate photos taken by the camera from temporary storage.

navigator.camera.cleanup( cameraSuccess, cameraError );
Description
Removes intermediate image files that are kept in temporary storage after calling camera.getPicture. Applies only when the value of Camera.sourceType equals Camera.PictureSourceType.CAMERA and the Camera.destinationType equals Camera.DestinationType.FILE_URI.

Supported Platforms
iOS
Example
navigator.camera.cleanup(onSuccess, onFail);

function onSuccess() {
    console.log("Camera cleanup success.")
}

function onFail(message) {
    alert('Failed because: ' + message);
}
