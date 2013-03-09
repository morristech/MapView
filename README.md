<p><strong>Update (March 8, 2013):</strong></p>

<p>While this component is still in beta, the version now available should behave predictably.</p>
<p>
  This commit entails a complete rewrite to more closely adhere to Android framework conventions, especially as regards layout mechanics.
  The changes were more significant than even a major version update would justify, and in practice this version should be considered an
  entirely new component.  Since we're still debugging I'm not bothering to deprecate methods or even make any attempt at backwards compatability;
  the previous version in it's entirely should be considered deprecated, and replaced with this release.
</p>
<p>
  I'm also moving documentation off github - it's now <a href="http://android-mapview.com">here</a>.
</p>

<p>An quick-n-dirty, undated, unversioned and incomplete changelog:</p>

<ol>
  <li>
    The component no longer requires (or even supports) initialization.  Rendering is throttled through handlers and managed directly
    via onLayout and event listeners.  This also means no more "onReady" anything - it just runs.  Just add some zoom levels and start using it.  In theory, zoom levels can be added dynamically later
    (after the code block it was instantiated - e.g., through a user action), but this is untested.
  </li>
  <li>
    Zoom levels are now managed in a TreeSet, so are naturally unique and ordered by area (so they can now be added in any order, and you're no
    longer required to add them smallest-to-largest)
  </li>
  <li>
    Image tiles now use caching, both in-memory (25% of total space available) and on-disk (up to 8MB).  For the on-disk cache, you'll now need to include
    external-write permission: <pre>&lt;uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" /&gt;</pre>.
    Testing for permission is on the todo list - right now you'll just get a runtime failure.
  </li>
  <li>
    Now supports multiple listeners (addMapEventListener instead of setMapEventListener).  Note that the interface is now MapEventListener, not OnMapEventListener.
    We now also provide access to a lot more events, including events for the rendering process and zoom events (where zoom indicates a change in zoom level, 
    in addition to the already existing events when the scale changed)
  </li>
  <li>
    We now intercept touch move events, so Markers won't prevent dragging.  The platform is still janky about consuming and bubbling events, but I think this is about as good as it's going to get.
  </li>
  <li>
    Removed support for downsamples.  That behavior wasn't visible in most other similar programs (e.g., google maps, CATiledLayer); it seemed to confuse some people; and it really hurt performance.
    If you want a low-res version, just stick an ImageView behind the MapView (in a FrameLayout or RelativeLayout), and size it to the size of your largest map.
  </li>
  <li>
    Removed support for tile transitions.  There was no way to keep this from eating up too much memory when abused (fast, repeated pinches between zoom levels), when we wanted to keep the previous
    tile set visible until the new one was rendered.
  </li>
  <li>
    Drastic refactorization and optimization of the core classes.  
  </li>
</ol>

<p>
  If you test this on a device, please let me know the results - in the case of either success or failure.  I've personally run it on several devices running several different versions of the OS, but
  would be very interested in results from the wild.  I'll be working on putting up a jar and a sample app.
</p>

<p>Finally, thanks to everyone that's been in touch with comments and ideas on how to make this widget better.  I appreciate all the input</p>


<h1>MapView</h1>
<p>The MapView widget is a subclass of ViewGroup that provides a mechanism to asynchronously display tile-based images,
 with additional functionality for 2D dragging, flinging, pinch or double-tap to zoom, adding overlaying Views (markers),
 multiple levels of detail, and support for faux-geolocation (by specifying top-left and bottom-right coordinates).</p>
 
 <p>It might be best described as a hybrid of com.google.android.maps.MapView and iOS's CATiledLayer, and is appropriate for a variety of uses
 but was intended for map-type applications, especially high-detail or custom implementations (e.g., inside a building).</p>
 
 <p>A minimal implementation might look like this:</p>
  
 <pre>MapView mapView = new MapView(this);
mapView.addZoomLevel(1440, 900, "path/to/tiles/%col%-%row%.jpg");</pre>
 
 A more advanced implementation might look like this:
 <pre>MapView mapView = new MapView(this);
mapView.registerGeolocator(42.379676, -71.094919, 42.346550, -71.040280);
mapView.addZoomLevel(6180, 5072, "tiles/boston-1000-%col%_%row%.jpg", 512, 512);
mapView.addZoomLevel(3090, 2536, "tiles/boston-500-%col%_%row%.jpg", 256, 256);
mapView.addZoomLevel(1540, 1268, "tiles/boston-250-%col%_%row%.jpg", 256, 256);
mapView.addZoomLevel(770, 634, "tiles/boston-125-%col%_%row%.jpg", 128, 128);
mapView.addMarker(someView, 42.35848, -71.063736);
mapView.addMarker(anotherView, 42.3665, -71.05224);
</pre>

<p>Javadocs are <a href="http://android-mapview.com">here</a>.</p>

<p>Licensed under <a href="http://creativecommons.org/licenses/by/3.0/legalcode" target="_blank">Creative Commons</a></p>