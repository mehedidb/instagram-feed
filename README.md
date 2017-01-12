Installation

Setting up Instafeed.js is pretty straight-forward. Just download the script and include it in your HTML:


<script type="text/javascript" src="path/to/instafeed.min.js"></script>
      

Instafeed.js also supports AMD/CommonJS:


// AMD
require(["path/to/instafeed"], function(Instafeed) {

});

// CommonJS
var Instafeed = require("instafeed.js");
      

NPM/Bower

Instafeed.js is also available on NPM and Bower:


npm install instafeed.js      # npm
bower install instafeed.js    # bower
      


Basic Usage

Here's how easy it is to get all images tagged with #awesome:


<script type="text/javascript">
    var feed = new Instafeed({
        get: 'tagged',
        tagName: 'awesome',
        clientId: 'YOUR_CLIENT_ID'
    });
    feed.run();
</script>
      

Instafeed.js with automatically look for a <div id="instafeed"></div> and fill it with linked thumbnails. Of course, you can easily change this behavior using the standard options. Also check out the advanced options and the section on templating for additional customization.

Requirements

The only thing you'll need to get going is a valid client id from Instagram's API. You can easily register for one on Instagram's website.

If you need help with that step, try Googling "How to get an Instagram client ID".

Standard Options

    clientId - Required. Your API client id from Instagram.
    accessToken - A valid oAuth token. Can be used in place of a client ID.
    target - The ID of a DOM element you want to add the images to.
    template - Custom HTML template to use for images. See templating.
    get - Customize what Instafeed fetches. Available options are:
        popular (default) - Images from the popular page.
        tagged - Images with a specific tag. Use tagName to specify the tag.
        location - Images from a location. Use locationId to specify the location.
        user - Images with a user. Use userId to specify the user. More info here.
    tagName - Name of the tag to get. Use with get: 'tagged'.
    locationId - Unique id of a location to get. Use with get: 'location'.
    userId - Unique id of a user to get. Use with get: 'user'.
    sortBy - Sort the images in a set order. Available options are:
        none (default) - As they come from Instagram.
        most-recent - Newest to oldest.
        least-recent - Oldest to newest.
        most-liked - Highest # of likes to lowest.
        least-liked - Lowest # likes to highest.
        most-commented - Highest # of comments to lowest.
        least-commented - Lowest # of comments to highest.
        random - Random order.
    links - Wrap the images with a link to the photo on Instagram.
    limit - Maximum number of Images to add.
    resolution - Size of the images to get. Available options are:
        thumbnail (default) - 150x150
        low_resolution - 306x306
        standard_resolution - 612x612


Advanced Options

    before - A callback function called before fetching images from Instagram.
    after - A callback function called when images have been added to the page.
    success - A callback function called when Instagram returns valid data. Takes the JSON data as an object argument.
    error - A callback function called when there is an error fetching images.Takes an error message as a string argument.
    mock - Fetch data without inserting images into DOM. Use with success callback.
    filter - A function used to exclude images from your results. The function will be given the image data as an argument, and expects the function to return a boolean. See the example below for more information.

Example Filter (get username + tagged):


<script type="text/javascript">
    var feed = new Instafeed({
        get: 'user',
        userId: 'USER_ID',
        filter: function(image) {
            return image.tags.indexOf('TAG_NAME') >= 0;
        }
    });
    feed.run();
</script>
      

To see a full list of properties that image has, see this thread on GitHub.

Templating

The easiest way to control the way Instafeed.js looks on your website is to use the template option. You can write your own HTML markup and it will be used for every image that Instafeed.js fetches.


<script type="text/javascript">
    var feed = new Instafeed({
        get: 'tagged',
        tagName: 'awesome',
        clientId: 'YOUR_CLIENT_ID',
        template: '<a href="{{link}}"><img src="{{image}}" /></a>'
    });
    feed.run();
</script>
      

Notice the {{link}} and {{image}}? The templating option provides several tags for you to use to control where variables are inserted into your HTML markup. Available keywords are:

    {{type}} - the image's type, can be image or video.
    {{width}} - the image's width, in pixels.
    {{height}} - the image's height, in pixels.
    {{orientation}} - the image's orientation, can be square, portrait, or landscape.
    {{link}} - URL to view the image on Instagram's website.
    {{image}} - URL of the image source. The size is inherited from the resolution option.
    {{caption}} - Image's caption text. Defaults to empty string if there isn't one.
    {{likes}} - Number of likes the image has.
    {{comments}} - Number of comments the image has.
    {{location}} - Name of the location associated with the image. Defaults to empty string.
    {{id}} - Unique ID of the image. Useful if you want to use iPhone hooks to open the images directly in the Instagram app.
    {{model}} - Full JSON object of the image. If you want to get a property of the image that isn't listed above you access it using dot-notation. (ex: {{model.filter}} would get the filter used)


Portrait and Landscape Photos

Until June 1, 2016, Instagram's API will return square images (with white borders), regardless of how they were originally uploaded.

If you'd like to get images in their original landscape and portrait forms, you can opt-in to the API change by editing your Instagram API client, and clicking on the "Migrations" tab:

Note: If you have the resolution option set to thumbnail (default), all images will be square regardless of your API settings.

Image Size Template Helpers

As of v1.4, Instafeed.js includes several helpers you can use in your template option to work with the new image sizes. These helpers are meant primarily to help control styling of the images through CSS.

    {{type}} - the image's type, can be image or video.
    {{width}} - the image's width, in pixels.
    {{height}} - the image's height, in pixels.
    {{orientation}} - the image's orientation, can be square, portrait, or landscape.


Pagination

As of v1.3, Instafeed.js has a .next() method you can call to load more images from Instagram. Under the hood, this uses the pagination data from the API. Additionall, there is a helper .hasNext() method that you can use to check if pagination data is available.

Together these options can be used to create a "Load More" button:


<script type="text/javascript">
    var loadButton = document.getElementById('load-more');
    var feed = new Instafeed({
        // every time we load more, run this function
        after: function() {
            // disable button if no more results to load
            if (!this.hasNext()) {
                loadButton.setAttribute('disabled', 'disabled');
            }
        },
    });

    // bind the load more button
    loadButton.addEventListener('click', function() {
        feed.next();
    });

    // run our feed!
    feed.run();
</script>
      


Getting images from your user account

To fetch images specifically from your account, set the get and userId options:


<script type="text/javascript">
    var userFeed = new Instafeed({
        get: 'user',
        userId: 'YOUR_USER_ID',
        accessToken: 'YOUR_ACCESS_TOKEN'
    });
    userFeed.run();
</script>
      

Note that YOUR_USER_ID corresponds to your Instagram account ID (eg: 4385108), not your username.

If you do not know your account ID, Google "What is my Instagram account ID?". There are several free tools available online that will look it up for you.
