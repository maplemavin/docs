# Home
Hello there! You are about to embark on an epic journey through Fullscreen Direct's Theming Engine documentation. It may sound scary, but you won't have any issues if you have a basic understanding of HTML, CSS, and/or JavaScript.

These docs aim to make using the Theming Engine super simple. If there is anything you'd like exposed in the Theming Engine that doesn't already appear to be available, please contact us about adding it for you.

If you learn better by diving into things, we have a bunch of example themes we've built in our [themes repo](https://github.com/stagebloc/themes) on GitHub that make a great starting point.

### General Information
In general, a theme consists of defining the pages that make sense for your website and filling them with data. Each `{page}` represents a separate URL on the site. The data within each `{page}` is built using any combination of `{module}`s and `{block}`s.

It is easiest to think of `{module}`s as `for` loops that loop through various data from your Fullscreen Direct account. Within a `{module}` a `{block}` can be used to represent a "model" of the data by exposing various pieces of information about that data.

Fullscreen Direct also then has various `{if}` statements and other helpful variables to make themes as customizable as possible.

The general structure of a theme is simply repeating chunks of code with the following structure. HTML can be placed anywhere inside of this hierarchy:

    {page:PageName}
        {module:ModuleName}
            {block:BlockName}
            {/block:BlockName}
        {/module:ModuleName}
    {/page:PageName}

#### Some useful notes...

* If you don't explicitly provide a favicon in the `<head>` of your HTML, we'll add the [default Fullscreen Direct favicon](https://www.fullscreendirect.com/favicon.ico) for you. To add your own favicon, simply upload it as a Theme Asset and put it in your theme's `<head>` HTML tag.

* We add various `<meta>` tags to your `<head>` content if they aren't explicitly set to aid in SEO. For instance, many tags from the [Open Graph Protocol](http://ogp.me/) are automatically added.

* We allow you to upload assets *(images, fonts, favicons, etc)* to our CDN and encourage you to use this when creating a theme. This means you don't have to host any part of your theme outside of Fullscreen Direct, making it easier for you.

* CSS and JS are both minified before being uploaded to our CDN so that you get the maximum performance and caching when users come to your site. Sometimes errors can occur during this process. If you notice your JS or CSS behaving differently once uploaded, please let us know.

#### Some tips...

* Use semicolons in JavaScript. As mentioned above, we do compress / minify your JavaScript before we upload it to our CDN so this will help ensure fewer issues occur.

* Remember to keep metadata in mind. For instance, when people share links to Facebook, you have control over the information Facebook pulls from the page. Check out this [obligatory StackOverflow post](http://stackoverflow.com/a/7623986/115629) for more info. Some useful links to get you started...
    - [Google Rich Snippets - Events](https://support.google.com/webmasters/answer/164506?hl=en)
    - [Knowledge Graph Tour Dates](http://googlewebmastercentral.blogspot.com/2014/03/musical-artists-your-official-tour.html)

### Help us out!
The documentation for the Theming Engine is up on GitHub for you to fork, modify, and improve. Join us over there to request features, add suggestions, and report bugs. What are you waiting for? [Git to it!](https://github.com/stagebloc/docs/blob/master/theming.md)

## Local Theme Dev
We've built an open source, PHP based tool to allow for easy localhost theme development. The tool simply uses our API to render a theme and return the HTML back to you. As such, a similar tool could be built in any other language pretty easily.

For the PHP version, head over to our [Local Theme Dev](https://github.com/stagebloc/local-theme-dev) repo on GitHub. Once installed and running, it allows developers to quickly and easily swap between different themes and accounts to get a feel for how various data looks in their theme.

Please feel free to open issues and/or submit pull requests if you find ways to make it more useful.

## Submitting Themes
Once you've built your theme, you can submit it to the Fullscreen Direct team for approval to be sold in our marketplace. This can be done via the Fullscreen Direct backend for your account. Themes can be priced as whatever amount you see fit (as well as free), and the same revenue share percentages apply to themes that apply to any other products sold on Fullscreen Direct. Once someone purchases your theme, they'll get the full source code and will be able to install and use it on their account.

# User Interaction
Understanding and being able to directly communicate with your fan base are among the most important processes in building a brand. At Fullscreen Direct, we pride ourselves in creating tools and functionality that make this as easy as possible. As such, we've created a couple of different ways to easily make sure this is the case.

## Profile Information
Prompting users to keep their profile information up to date is a great way to ensure you get the complete picture of your fan base. One way to do this is to include the JavaScript below on your hosted site. When present, this post message ping will cause an SBNav modal to show up prompting the user for their information if we determine it may be out of date.

```
window.addEventListener('load', function load() {
    window.removeEventListener('load', load, false);
    pm({target: window.frames.sbnav, type: 'sbUserInformationPrompt', data: {} });
}, false);
```

## Email Verification
Having users verify their email address is one of the most important steps in being able to directly contact them. In order to ensure our deliverability rates remain high, we only email users who have verified their email addresses by clicking a verification link sent to them.

Users will automatically be sent an initial link on signup. They'll also be sent a reminder email a couple days after signup if they still haven't verified their email address. Lastly, SBNav will show a nag modal for all logged in users until they verify their email address.

If you want a more custom experience for your fans, simply using the `{if:UserIsVerified}` in your theme will disable our pre-built SBNav nag. Using this, you could, for instance, show a custom button that resends a verification email to user. The JavaScript to do so (which is part of SBNav's advanced functionality) is below.

`pm({ target: window.frames.sbnav, type: 'sbInlineSendVerificationEmail', data: {} });`

# Syntax
The Fullscreen Direct Theming Engine makes use of curly brackets and a few main building blocks for the majority of its syntax. The building blocks (page, modules, and blocks) should be kept lowercase like they are in these docs.

To find out more information about each building block, navigate to them in the navigation on the left hand side of this page.
    
## Pages
Each view (i.e. URL) on Fullscreen Direct is contained within a `{page}`. There are a bunch of built in pages, but you can also create your own custom pages (see below). Any HTML, `{module}`s, or `{block}`s can be put inside of a `{page}`.

It is not necessary to define a view for every `{page}` available via the Theming Engine. Define pages that make sense for your theme, and the rest will be taken care of via a fallback theme we've created so that no links will be broken.

A page in its simplest form would look like the following block of code:

    {page:PageName}
        ...
        HTML content, etc here
        ...
    {/page:PageName}
    
### Custom Pages
Custom pages allow you to create pages at URLs that aren't already built into the Theming Engine. They can be defined using the following syntax:

    {page:CustomMyAwesomePage}
        ...
        HTML content, etc here
        ...
    {/page:CustomMyAwesomePage}

The URL for a custom page is simply whatever follows "Custom" in the page's definition (the above examples URL would be `/MyAwesomePage`). Using slashes within the page's definition is also allowable and allows easy creation of hierarchical URLs.

Any content that can be put inside of a built in page can be put inside of a custom page.
    
### Page Options
Pages can have options applied to them. Options are listed within the `{page}` code itself like below:

    {page:PageName optionName="optionValue"}
        ...
        HTML content, modules, etc here
        ...
    {/page:PageName}

The following options are available for `{page}`s:

`homepage`

    a flag representing if the page should be used as the homepage

    accepted values are true or false

    the default homepage is ActivityStreamList *if that page isn't defined the first defined Page in the theme's HTML will be used*

`url`

    a URL to override the page's default URL

    accepted values are any string valid in a URL, the [%id] is always assumed to be at the end *(Note: do not start the override URL with a slash (/))*

        {page:EventList url="shows"}
            <!-- The events page would now be at /shows, not /events -->
        {/page:EventList}

    default value is based on the page or whatever comes after "Custom" for custom pages

    if a page has more than one [%id] (i.e. `BlogCommentView`), use an asterisk (*) for the first [%id]

`title`

    a value to force the <title> of the <head> content of the page to be

    accepts any string

    default value will be based on the `{module}`s present on the rendered page

`ajax`

    a GET parameter value that can be used to output ONLY the stuff within a specific page (i.e. strip out any headers, footers, etc) *note: this is useful for infinite scrolling with AJAX*

    accepted values are true and false

    default value is false
    
`searchable`

    whether or not this page should be able to be indexed by search engines
    
    accepted values are true and false
    
    defaults to true
    
## Modules
Modules are the main data loading structures within `{page}`s. The general syntax for modules is as follows:

    {module:ModuleName}
        ...
        HTML content, blocks, etc here
        ...
    {Else:ModuleName}
        ...
        Data to show if the module doesn't return any
        ...
    {/module:ModuleName}

Modules come in two types, views and lists. A listing module will iterate through numerous pieces of content and repeat the content within the module as many times as it finds pieces of data.

Using `{Else:<module_name>}` will cause the content in the else section to render if the module doesn't load any data.

### Module Options
Modules can have their own options. Some of the options are available to all `{module}`s while other are specific to particular `{module}`s. The syntax for module options is the exact same as that of `{page}`s and looks as follows:

    {module:ModuleName optionName="optionValue"}
        ...
        HTML content, blocks, etc here
        ...
    {/module:ModuleName}
    
The following options are an example of two options that are available on most listing modules. All of the available options for a particular `{module}` can be found by the documentation for that specific modules elsewhere in these docs.

`pagination`

    flag representing if the module should be used as the primary module for pagination on the parent {page}

    accepted values are true or false

    defaults to what the Theming Engine decides is best

`ignorePaging`

    available on most listing modules to make the module ignore any pagination that might be preset in the header

    accepted values are true or false

    defaults to false
    
### Module If Statements
**if:PositionInModule**  
Checks to see what block is currently being rendered in the listing module. The first item in the module is `1` (i.e. it is 1 indexed)

`gt`

    check if the position is greater than a certain point
    
    accepted values are any integer
    
    defaults to none

`lt`

    check if the position is less than a certain point

    accepted values are any integer

    defaults to none

`e`

    check to see if the position is equal to a certain point

    accepted values are any integer

    defaults to none

`multipleOf`

    check to see if the position is a multiple of a number

    accepted values are any integer

    defaults to none

## Blocks
Blocks can be best thought of as the models of the data and must be inside of a `{module}`. When inside of a listing `{module}`, a `{block}` will be repeated as many times as necessary depending on the returned data. The general syntax for `{block}`s is as follows:

    {block:BlockName}
        ...
        HTML content, block variables, etc here
        ...
    {/block:BlockName}
    
Depending on the block you are using, different data variables become available that allow you to represent the object on the page.

## Variables
Variables are the simplest of Theming Engine elements and return the finest level of detail about an object. Using a variable simply looks like the follow:
    
    {VariableName}
    
Using options with a variable is just like using options with any other Theming Engine elements:
    
    {VariableWithOption optionOneName="optionOneValue" optionTwoName="optionTwoValue"}
    
All variables have the `block` option available to them to force them to render within the context of a certain `{block}`. This usually isn't necessary unless you have two nested `{block}`s and want to make sure the variables renders at the correct time.

### Custom Variables
You can use custom variables to define chunks of code to reuse throughout the rest of your theme. This can be useful when repeating layout elements on your site on some pages, but not others.

To define a variable, use the following syntax:

    {variable:CustomMyVariable}
        ...
        Any content here
        ...
    {/variable:CustomMyVariable}

Custom variables must start with "Custom" in their names. In this example, your variable name would be `MyVariable`. After defining your variable, you can access it just like a normal variable. For instance, to use the above variable we just defined, you'd write the following anywhere in your theme:

    {CustomMyVariable}

### {option} Variables
You can set up ways for Fullscreen Direct account admins to customize their theme to their liking without them doing any HTML/CSS/JS editing by using `{option}` variables. Options variables are pulled into the sidebar theme editor on the web and presented using `<form>` elements that less technical users can use to customize their theme. The general syntax for an `{option}` variable is:

    {option:VariableType
        group="VariableGroup"
        name="Variable Name"
        default="Default Value"
        format=""
        defaultFormat=""}

Keep in mind that less is often more. If someone wants very specific, fine tune control, it's best if they just edit the theme.

The following parameters are available to all `{option}` variables:

`name` (*required*)

    The unique name for this variable. This name is also presented to the user in the sidebar theme editor.

`group`

    A namespace for this variable. This is used to group variables in the sidebar theme editor.

`format`

    The way the data is outputted. This makes use of PHP's [sprintf()](http://php.net/sprintf) function if you'd like to force a format.

`defaultFormat`

    The default format to be used for the data.

`default`

    When the user doesn't explicitly set or change this option, this value will be used.

The following `{option}` variables are currently available for use:

**option:Image**

This `{option}` variable allows users to upload an image to use within their theme (i.e. a header image). This shouldn't be used for things like sprites or other theme assets. A file upload box will show up in the theme editor sidebar where they can upload JPEG, GIF, and PNG files to be used in the theme. The data returned is the full URL to the image uploaded.

    {option:Image
        group="Images"
        name="Header Image"
        format="background-image: url(%s);"
        defaultFormat=""}

**option:Color**

This `{option}` variable will present users with a color select box in the theme sidebar. The user can then select a color to use (i.e. font color). The additional parameter of `rgb` is available for this option. The data returned will be the full HEX value with the pound sign or the colors, comma separated (see `rgb` parameter below).

`rgb`

    Determines whether or not to return RGB or HEX values
    
    accepted values are true or false
    
    defaults to false

An example:

    {option:Color
        group="Colors"
        name="Footer Color"
        format="color: rgb(%s);"
        defaultFormat=""}

**option:Select**

This `{option}` variable adds a drop down box containing a list of predefined values to the sidebar. The data returned is the value selected.

`presets`
    Defines a comma separated list of values

An example:

    {option:Select
        group="Images"
        name="Background Repeat"
        format="background-repeat: %s;"
        presets="repeat,no-repeat,repeat-x,repeat-y"
        default="no-repeat"
    }

**option:Textarea**

This `{option}` variable presents users with a textbox where they can input any string. The data returned is the user's string.

## If Statements
If statements will check if a certain statement is true or not and add the appropriate code as defined within the theme. Else statements can be achieved by placing `{if:Else}` inside of an if statement. For example, an if statement in its simplest form could be as follows:

    {if:IfStatementName}
        ...
        This content will be used if the statement is true
        ...
    {if:Else}
        ...
        This content will be used if the statement is false
        ...
    {/if:IfStatementName}

If statements can be nested inside of one another as well.

### If Statement Options
Various if statements have different options available to them depending on the statement. The syntax is exactly the same as `{pags}`s and `{module}`s:

    {if:IfStatementName optionName="optionValue"}
        ...
        This content will be used if the statement is true
        ...
    {/if:IfStatementName}
    
An option that is available to all if statements is `block`. Sometimes for if statements that can be used in numerous different `{block}`s where weird behavior is happening because of the nested, you can use the `block` option to have finer control over when the if statement is rendered.

`block`

    the {block} to force the if statement to render as a part of

    accepted values any parent {block} of the if statement

    defaults to the theming engines best guess

### Combined If Statements
If statements support the use of `||` and `&&` for AND and OR logic. However, mixing of ANDs and ORs is not currently allowed (i.e. it must be all ANDs or all ORs). For example:

    {if:IfStatementOne||IfStatementTwo}
        ...
        This content will be used if either of the if statements are true
        ...
    {/if:IfStatementOne||IfStatementTwo}
    
# Sections
The main sections of the Theming Engine line up with the available content you can create and share on Fullscreen Direct.

In general, the documentation for each section goes from higher level to lower level. It will first list the pages. After the pages, the modules are listed with their available options. The block is listed after the modules and can be used inside of any of the modules for that section. After the definition of the block, all of the variables (and variables with options) available to that block specifically are listed.

Lastly, the if statements are listed for that block as well. Unless otherwise noted, if statements must be used inside of the corresponding block *(some are global and can be used anywhere)*.

## General
The following Theme Engine elements are available for general use within a theme and can be placed anywhere.

### Variables
    <!-- Example use of some global variables -->
    <head>
        <title>{AccountName}</title>
        {CSS}
        {JS}
    </head>

`CSS`

    a <link rel="stylesheet" /> tag with a link to the theme's CSS on Fullscreen Direct's CDN

`JS`

    a <script src=""> tag with a link to the theme's JS on Fullscreen Direct's CDN

`Body-ID`

    the general section of the page (useful for general section styling)

`AccountName`

    the name of the account

`AccountId`

    the ID of the account
    
`jPlayer`

    the jPlayer javascript library *(requires {jQuery} to be present before this variable)*
    
`Link-StageBloc`

    a link to www.fullscreendirect.com

### Variables With Options
**jQuery**  
The latest version of jQuery on the page via Google's CDN

`v`

    use this parameter to specify a particular version
    
    accepted values are any version hosted on Google
    
    defaults to the latest version

**PositionInModule**  
Get a module's current loop position. By default it is one-indexed.

`start`

    use this parameter to specify a start position (ie, change to be zero-indexed)
    
    accepted values are any integer
    
    defaults to 1

**BootstrapCSS**  
The latest version of Bootstrap's combined (responsive with icons) CSS from [BootstrapCDN](http://www.bootstrapcdn.com/)

`v`

    use this parameter to specify a particular version
    
    accepted values are any version hosted on BootstrapCDN
    
    defaults to the latest version

`includeIcons`

    whether or not to includes Bootstrap's icons with the CSS

    allowed values are true or false
    
    defaults to true
    
**BootstrapJS**  
The latest version of Bootstrap's JS from [BootstrapCDN](http://www.bootstrapcdn.com/)

`v`

    use this parameter to specify a particular version
    
    accepted values are any version hosted on BootstrapCDN
    
    defaults to the latest version
    
**SecureEmail**  
Encodes an email address to protect it from spambots

`address` *(required)*

    the email address to encode
    
    accepted values are any string
    
    defaults to none
    
`text`

    the text to put inside the `<a>` tag
    
    accepted values are any string
    
    defaults to value for address
    
**ChildAccountIDs**  
A comma separated list of children account IDs of the current account, useful with the `accountid` parameter of some modules

`type`

    the type of accounts to limit the results to

    accepted values are any comma separated combination of children account types you've defined

    defaults to all types

`includeCurrent`

    whether or not to include the current account with the children

    accepted values are true or false

    defaults to false
    
### If Statements
**if:OptionIsSet**  
Determines if the given `{option}` variable has been set or not

`group` *(required)*

    the group this option belongs to
    
    accepted values are any string
    
    defaults to none

`name` *(required)*

    the name of the option in the specified group
    
    accepted values are any string
    
    defaults to none

**if:PageIsActive**  
Checks to see if a page is the currently rendered theme engine page

`page`
    
    the page to check *should be anything after the colon in {page:<Page_Name>}*
    
    accepted values are any page
    
    defaults to none

`uri`

    if you need to be more exact, you can specify an exact URI *the part of the URL after your account name (non custom domains) or you custom domain, no leading forward slash*
    
    accepted values are any URI
    
    defaults to none
    
**if:PaginationIs**  
Checks if the pagination is within a certain bound

`gt`
    
    check if the pagination is greater than a certain page
    
    accepted values are any integer
    
    defaults to none

`lt`

    check if the pagination is less than a certain page

    accepted values are any integer

    defaults to none

**if:HasTags**  
Checks to see if a piece of content has tags or not

**if:AccountLikedContent**  
Checks to see if an admin of the account liked the content in question

`userId`

    the user ID of a specific admin to check for
    
    accepted values are any user ID that is an admin for the account
    
    defaults to none
    
`contentType`

    the content type slug of the content to check

    accepted values are {ContentType-Audio}, {ContentType-Blog}, {ContentType-Events}, {ContentType-Photos}, {ContentType-Statuses}, {ContentType-Videos}, {ContentType-Store}

    defaults to whatever the current item is in the Theming Engine

`contentId`

    the content ID of the content to check

    accepted values are {ContentType-Audio}, {ContentType-Blog}, {ContentType-Events}, {ContentType-Photos}, {ContentType-Statuses}, {ContentType-Videos}, {ContentType-Store}

    defaults to whatever the current item is in the Theming Engine

### Pages
**page:Error404**  
`/AnyPageThatDoesNotExist` - This page will be loaded whenever a unknown URL structure is hit

## Modals
As described in the SBNav section, there is a lot of built in functionality for you to use as a theme developer. Much of that functionality is accomplished through modals we've created and made available to use through SBNav. These modals are meant to handle a lot of the common actions a user may want to do and makes it so that you don't have to build that functionality into your theme if you don't want to.

### {CommentLink}
A link that opens a modal and allow the user to view content and comment on it

`linkText`

    the text to be put inside the <a> tag 

    accepted values are any string (HTML included)

    defaults to "Add Comment"

`href`

    the link to fallback to in case the JS fails opening the modal
    
    accepted values are any URL
    
    defaults to "javascript:void(0)"

`closeTag`

    whether or not to close the <a> tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the <a> tag
    
    accepted values are any string
    
    defaults to none

`contentType`

    the content type slug of the content to like

    accepted values are {ContentType-Audio}, {ContentType-Blog}, {ContentType-Events}, {ContentType-Photos}, {ContentType-Statuses}, {ContentType-Videos}, {ContentType-Store}

    defaults to whatever the current item is in the Theming Engine

`contentId`

    the content ID of the content to like

    accepted values are {ContentType-Audio}, {ContentType-Blog}, {ContentType-Events}, {ContentType-Photos}, {ContentType-Statuses}, {ContentType-Videos}, {ContentType-Store}

    defaults to whatever the current item is in the Theming Engine

### {ContestEntryModalLink}
A link that opens a modal and allows a user to enter a contest

`contestId`

    the ID of the contest

    accepted values are any contest ID that belongs to the account

`tag`

    the HTML element to be used for the signup action

    accepted values are any HTML element

    defaults to "a"

`text`

    the text to be put inside the tag

    accepted values are any string (HTML included)

    defaults to "Enter Contest"

`threshholdText`

    the text to be put inside the tag when the contest threshold of entries has been met

    accepted values are any string (HTML included)

    defaults to "Contest Closed"

`comingSoonText`

    the text to be put inside the tag when the contest has not started yet

    accepted values are any string (HTML included)

    defaults to "Contest Coming Soon"

`enteredText`

    the text to be put inside the tag when someone has entered the contest but it is not yet over

    accepted values are any string (HTML included)

    defaults to "Contest Entered"

`loserText`

    the text to be put inside the tag when someone has lost

    accepted values are any string (HTML included)

    defaults to "You Did Not Win"

`winnerText`

    the text to be put inside the tag when someone has won

    accepted values are any string (HTML included)

    defaults to "Contest Winner!"

`closeTag`

    whether or not to close the tag

    accepted values are true or false

    defaults to true

`class`

    the class to assign to the <a> tag

    accepted values are any string

    defaults to none

### {SignupLink}
A link that opens a modal and allows a user to sign up for Fullscreen Direct and follow your account

`tag`

    the HTML element to be used for the signup action

    accepted values are any HTML element

    defaults to "a"

`text`

    the text to be put inside the tag

    accepted values are any string (HTML included)

    defaults to "Sign Up"

`closeTag`

    whether or not to close the tag

    accepted values are true or false

    defaults to true

`class`

    the class to assign to the <a> tag

    accepted values are any string

    defaults to none

### {LoginLink}
A link that opens a modal and allows a user to log in to their Fullscreen Direct profile

`tag`

    the HTML element to be used for the login action

    accepted values are any HTML element

    defaults to "a"

`text`

    the text to be put inside the tag

    accepted values are any string (HTML included)

    defaults to "Login"

`closeTag`

    whether or not to close the tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the HTML element
    
    accepted values are any string
    
    defaults to none

### {EditUserNotificationSettingsModalLink}
A link that opens a modal and allows a user to edit their notification settings.

`text`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Edit Notification Settings"

`closeTag`

    whether or not to close the <a> tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the <a> tag
    
    accepted values are any string
    
    defaults to none

### {EditUserProfileModalLink}
A link that opens a modal and allows a user to edit their user profile.

`text`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Edit Profile"

`closeTag`

    whether or not to close the <a> tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the <a> tag
    
    accepted values are any string
    
    defaults to none

### {EditUserSettingsModalLink}
A link that opens a modal and allows a user to edit their user settings.

`text`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Edit Settings"

`closeTag`

    whether or not to close the <a> tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the <a> tag
    
    accepted values are any string
    
    defaults to none

### {FanClubMembershipManagement}
A link that opens a modal and allows a user to view details about their membership on your fan club. This information includes what tier they are on and when their next payment may be due among other things.

`text`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "My Membership"

`closeTag`

    whether or not to close the <a> tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the <a> tag
    
    accepted values are any string
    
    defaults to none

### {JoinFansiteLink}
A link that opens a modal and allows a user to join the site's fan club *(the account must have a fan club setup for this to work)*

`text`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Join Fansite"

`closeTag`

    whether or not to close the <a> tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the <a> tag
    
    accepted values are any string
    
    defaults to none
    
`reload`

    whether or not to reload the page after their join the fansite
    
    accepts values are true and false
    
    defaults to false

`forceFreeTier`

    the tier you want to force a joining of (which override the join fansite modal) *(requires that the tier you pass be free)*
    
    accepted values are 1, 2, or 3 assuming the tier is free
    
    defaults to false

`view`

    what to default the view to if the user isn't logged on

    accepted values are "login" or "signup"

    defaults to "login"

### {SubmitFanContentLink}
Creates a link that, when clicked, will open a modal to allow fans to submit content

`linkText`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Add Content"

`closeTag`

    whether or not to close the <a> tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the <a> tag
    
    accepted values are any string
    
    defaults to none

`contentType`

    the type of content to default the content adding modal to

    accepted values are status, blog, photo, or video

    defaults to status

### {ShareLink}
Add a link to open the built in Fullscreen Direct sharing modal. When the user is logged out this will have generic social sharing buttons for services like Twitter and Facebook. When the user is logged in, content can be shared using their connected social accounts.

`linkText`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Share"

`shareText`

    the text to default to in the sharing modal (other than the link to the content being shared)

    accepted values are any string

    defaults to none

`closeTag`

    whether or not to close the <a> tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the <a> tag
    
    accepted values are any string
    
    defaults to none

### {ReportContentLink}
Creates a link that, when clicked, will open a modal for reporting (flagging) content as inappropriate, etc

`linkText`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Report Content"

`closeTag`

    whether or not to close the <a> tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the <a> tag
    
    accepted values are any string
    
    defaults to none

`contentType`

    the content type slug of the content being reported

    accepted values are {ContentType-Audio}, {ContentType-Blog}, {ContentType-Events}, {ContentType-Photos}, {ContentType-Statuses}, {ContentType-Videos}, {ContentType-Store}

    defaults to whatever the current item is in the Theming Engine
    
`contentId`

    the content ID of the content being reported

    accepted values are {ContentType-Audio}, {ContentType-Blog}, {ContentType-Events}, {ContentType-Photos}, {ContentType-Statuses}, {ContentType-Videos}, {ContentType-Store}

    defaults to whatever the current item is in the Theming Engine

### {OpenCartLink}
Creates a link that, when clicked, will open the user's cart

`text`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Open Cart"

`closeTag`

    whether or not to close the <a> tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the <a> tag
    
    accepted values are any string
    
    defaults to none

## Functionality

### {LikeLink}
A link that likes a specific piece of content

`likeText`

    the text to be put inside the <a> tag when the item has not yet been liked

    accepted values are any string (HTML included)

    defaults to "Like"

`unlikeText`

    the text to be put inside the <a> tag when the item has been liked

    accepted values are any string (HTML included)

    defaults to "Unlike"

`closeTag`

    whether or not to close the <a> tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the <a> tag
    
    accepted values are any string
    
    defaults to none

`contentType`

    the content type slug of the content to like

    accepted values are {ContentType-Audio}, {ContentType-Blog}, {ContentType-Events}, {ContentType-Photos}, {ContentType-Statuses}, {ContentType-Videos}, {ContentType-Store}

    defaults to whatever the current item is in the Theming Engine

`contentId`

    the content ID of the content to like

    accepted values are {ContentType-Audio}, {ContentType-Blog}, {ContentType-Events}, {ContentType-Photos}, {ContentType-Statuses}, {ContentType-Videos}, {ContentType-Store}

    defaults to whatever the current item is in the Theming Engine

### {DeleteLink}
A link that deletes a specific piece of content (if the user is authorized)

`linkText`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Delete"

`closeTag`

    whether or not to close the <a> tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the <a> tag
    
    accepted values are any string
    
    defaults to none

`contentType`

    the content type slug of the content to delete

    accepted values are {ContentType-Audio}, {ContentType-Blog}, {ContentType-Events}, {ContentType-Photos}, {ContentType-Statuses}, {ContentType-Videos}, {ContentType-Store}

    defaults to whatever the current item is in the Theming Engine

`contentId`

    the content ID of the content to delete

    accepted values are {ContentType-Audio}, {ContentType-Blog}, {ContentType-Events}, {ContentType-Photos}, {ContentType-Statuses}, {ContentType-Videos}, {ContentType-Store}

    defaults to whatever the current item is in the Theming Engine

## Linking
There are a few tools available to linking to various parts within your site and outside of your site. For general site navigation it is recommended to use `{module:Navigation}`.

### {Link}
A variable that allows for linking to various functionality, pages on your hosted site, and pages on Fullscreen Direct (i.e. `{Link to="page"}`)

`to`

    where you want to link to

    available options include:
        TOS - Fullscreen Direct's Terms of Service
        PrivacyPolicy - Fullscreen Direct's privacy policy
        Contact - Fullscreen Direct's contact page
        StageBloc - Our homepage
        ForgotPassword - Fullscreen Direct's forgot password page
        Signup - Fullscreen Direct's sign up page
        ConnectTwitter - A link that will OAuth connect a user's Twitter to Fullscreen Direct
        ConnectFacebook - A link that will OAuth connect a user's Facebook to Fullscreen Direct
        DisconnectTwitter
        DisconnectFacebook
        CurrentPage - A link to the current page the user is on

### {RedirectTo}
A variable that can be used for redirecting a page to server else server side

`url`

    the URL to redirect to

## About
### page:About
`/about` - This page should show general information about your account

### module:AccountAbout
This module loads information for an account

**Module Options**  
`accountId`

    an alternative account ID to use other than the current one
    
    accepted values are any child account ID
    
    defaults to the current account

`requireDescription`

    whether or not the account to load should have a non-empty description to require this module to load data
    
    accepted values are true or false
    
    defaults to true

### block:AccountAbout
This block exposes information about an account

AccountAboutName  
:    the name of the account

AccountAbout  
:    the biography text of the account

AccountAboutStageBlocUrl  
:    the Fullscreen Direct URL for this account

AccountAboutCleaned  
:    the bio text stripped of all tags except `<span><em><strong><a><u><i><b>`

AccountAboutUrl  
:    the URL to the account about page

### Variables With Option
**AccountPhotoUrl** *(global, can be used anywhere)*  
The main image for this account

`size`

    the size of the photo to load
    
    accepted sizes are "thumbnail", "small", "medium", "large", "original"
    
    defaults to "thumbnail"
    
`accountId`

    the ID of the account to show the photo for

    accepts any account ID

    defaults to the photo for the current account
    
`defaultPhoto`

   a photo URL to use if the account photo doesn't exist
   
   accepts an URL
   
   defaults to none
    
## Pagination
The pagination module can be used in most all listing modules.

### module:Pagination
This module latches onto the last seen module and provides next/previous links to go through the pages. *(This currently must be the first module **after** the module you wish to paginate)*

    {module:Pagination}
        <div id="pagination">
            {block:NextPage}
                <a href="{NextPage}">Older</a>
            {/block:NextPage}
            {block:PreviousPage}
                <a href="{PreviousPage}">Newer</a>
            {/block:PreviousPage}
        </div>
    {/module:Pagination}

### block:PreviousPage
PreviousPage  
:   link to previous page

### block:NextPage
NextPage  
:   link to next page

## Navigation
    {module:Navigation ignore="{ContentType-Audio}" events="Shows" order="events,about,customrandompage"}
    <ul>
        {block:NavigationItem}
            <li><a href="{Url}" class="{CSSClass}">{LinkText}</a></li>
        {/block:NavigationItem}
    </ul>
    {/module:Navigation}

### module:Navigation
This module will list all of the links to various content sections that the account supports

**Module Options**  
`ignore`

    a comma separated list of sections to ignore
    
    accepted values are {ContentType-Audio}, {ContentType-Blog}, {ContentType-Events}, {ContentType-Photos}, {ContentType-Statuses}, {ContentType-Videos}, {ContentType-Store}, updates, about, fansite
    
    defaults to none
    
`order`

    a comma seperated list of the order for the navigation, when using custom pages be sure to prefix it with "custom" (see example above)
    
    accepted value are the same as ignore
    
    defaults to general ordering
    
`fansiteNavigation`

    whether or not to show the subnvagiation for fansite as opposed to the global nav
    
    accepted values are true and false
    
    defaults to false
    
`blogCategoryNavigation`

    whether or not to show the subnavigation for blog categories as opposed to the global nav
    
    accepted values are true and false
    
    defaults to false
    
`photoAlbumCategoryNavigation`    

    whether or not to show the subnavigation for photo categories as opposed to the global nav
    
    accepted values are true and false
    
    defaults to false
    
`includeAll`

    whether or not to include "All" as a category for the blog and store
    
    accepted values are true and false
        
    defaults to false
    
### block:Navigation
This block exposes various options available for each navigation link

CSSClass  
:   a class that says what the navigation item is linking to, will automatically append a class `active` if the current navigation item

LinkText  
:   the text for the navigation link

Url  
:   the URL for the navigation link

### If Statements
`if:NavItemHasSlug`

    check to see if the currently rendered item is a current page or not
    
    `slug` (*required*)
    
        the slug in question
        
        accepted values are anything you'd pass to `order` or `ignore`
        
        defaults to none

## Fan Clubs

### module:FansiteAbout
This modules loads content related to a fan club

### block:FansiteAbout
This block exposes various content available for a fan club

FansiteTitle  
:   the title of the fan club

FansiteDescription  
:   the description of the fan club

FansiteTier1Name  
FansiteTier2Name  
FansiteTier3Name  
:   the title of the fan club tier

FansiteTier1Description  
FansiteTier2Description  
FansiteTier3Description  
:   the description of the fan club tier

FansiteTier1Price  
FansiteTier2Price  
FansiteTier3Price  
:   the price of the fan club tier

## Fan Content Stream
### page:Fansite
`/fansite` - This page should show a listing of fan content posted to your fan club

### module:FansiteContentList
This module lists all content posted by fans to a fan club

`supported` *(required)*

    a comma separated list of supported content types

    accepted values are {ContentType-Audio}, {ContentType-Blog}, {ContentType-Photos}, {ContentType-Statuses}, {ContentType-Videos}

    defaults to none

`includeAccountcontent`

    whether or not to include account content

    accepted values are true or false

    defaults to false
    
`userId`

    a user ID to filter the content by
    
    accepted values are the ID of any fans
    
    defaults to none
    
`limit`

    the amount of blogs to list per page

    accepted values are any integer

    defaults to 5

`offset`

    skips X number of items based on the ordering, it is automatically adjusted when paging

    accepted values are any integer

    defaults to 0
    
### block:FansiteContentView
This block exposes various content available for fan content

FansiteContentAuthorName  
:   the name of the fan who created the content

FansiteContentAuthorUrl  
:   the URL to the user page for the author of this content

FansiteContentAuthorUsername  
:   the username of the user who created the content

FansiteContentAuthorId  
:   the ID of the fan who created the content

FansiteContentAuthorThumbnailPhotoUrl  
:   a thumbnail photo URL of the fan who created the content

FansiteContentTitle  
:   the title of the content

FansiteContentID  
:   the ID of the content

FansiteContentSection  
:   the content section slug of the content

FansiteContentBody  
:   the body of the content

FansiteContentCSSClass  
:   a CSS class to use for the content

FansiteContentExcerpt  
:   an excerpt of the content

FansiteContentExcerptCleaned  
:   an excerpt of the content with most of the HTML tags stripped

FansiteContentExcerptStripped  
:   an excerpt of the content with all of the HTML tags stripped

FansiteContentTagCount  
:   the number of tags for the piece of content

FansiteContentUrl  
:   the URL to the original content

FansiteContentShortUrl  
:   the short URL to the content's individual page

FansiteContentPhotoCount  
:   the number of photos related to the content

FansiteContentLikeCount  
:   the number of likes the content has

CommentCount
:   the number of comments the content has

### Variables With Options
**ActivityDate**  
The date this content item was published
  
`format`

    the format of the date according to PHP [date() function](http://php.net/date) 

    accepted values are a date format string, relative (returns time ago such as "5 seconds ago"), or gmdate (a GMT date in PHP date format 'Y-M-d h:i A')

    defaults to `n/j/y`

**ActivityExcerpt**  
A trimmed version of `{FansiteContentBody}`

`length`

    the length of the excerpt to use
    
    accepted values are any integer
    
    defaults to 600
    
`cleaned`

    whether or not to remove all tags except `<span><em><strong><a><u><i><b>`
    
    accepted values are true and false
    
    defaults to false
    
`considerHtml`

    whether or not to include HTML tags as page of the length
    
    accepted values are true and false
    
    defaults to true
    
`ending`

    what to use at the end of the content if it is cut short
    
    accepted values are any string
    
    defaults to "..."

**ActivityPhotoUrl**  
A cover image for this content or a default one if there is no cover set

`size`

    the size of the photo to load
    
    accepted sizes are "thumbnail", "small", "medium", "large", "original"
    
    defaults to "thumbnail"
    
### If Statements
`if:BlogPostHasPhoto`

    check if a blog post has at least one photo (`<img>` tag) in it


## Activity Stream
### page:ActivityStreamList
`/updates` - This page should show a listing of content posted by your account *(this is the default homepage)*

### module:ActivityStreamList
This module lists all content posted by an account. Content is returned by the date it was created.

**Modules Options**  
`supported` *(required)*

    a comma separated list of supported content types *note: if you are only using one filter type here, please use the actual module for that specific type of content instead*

    accepted values are {ContentType-Audio}, {ContentType-Blog}, {ContentType-Events}, {ContentType-Photos}, {ContentType-Statuses}, {ContentType-Videos}, {ContentType-Store}

    defaults to none

`groupPhotos`

    whether or not photos should be grouped into album udpates

    accepted values are true and false

    defaults to true

`exclusive`

    whether or not to also show exclusive content if the user is logged in and capable of seeing it

    defaults to true
    
    accepted values are true, false, always *("always" will always show it, even for non-authorized users)*, or a comma-separated list of fan club tier(s) to display.

`sticky`

    whether or not to include sticky content

    defaults to showing both

    accepted values are true (to show only sticky posts) or false (to show only non-sticky posts)

`ignoreSticky`

    whether or not sticky blog posts should be on top of the listing

    accepted values are true or false

    defaults to false
        
`limit`

    the amount of blogs to list per page

    accepted values are any integer

    defaults to 5

`offset`

    skips X number of items based on the ordering, it is automatically adjusted when paging

    accepted values are any integer

    defaults to 0

### block:ActivityStreamView
This block exposes various content available for a account content

ActivityAuthorName  
:   the name of the person who created the content

ActivityAuthorPhotoUrl  
:   a URL to a 130x130px thumbnail user photo

ActivityTitle  
:   the title of the content

ActivityId  
:   the ID of the content

ActivityBody  
:   the main content for the activity, varies based content type

    Blog: the full blog post HTML
    Event: the same as {ActivityTitle}: [%number] events added today!
    Statuses: full status content
    Photos albums: album description
    Videos: video embed code
    Video playlists: playlist description
    Audio: Link to streamable MP3
    Audio playlists: playlist description
    Store items: item description

ActivityCSSClass  
:   the relevant class from this list: repost, blog, blog repost, status, event, video, audio, photo

ActivityUrl  
:   the permalink to the content's individual page

ActivityShortUrl  
:   the short URL to the content's individual page

ActivityTagCount  
:   the number of tags this piece of content has

ActivityLikeCount  
:   the number of likes this piece of content has

ActivityPhotoCount  
:   number of photos added to a photo album item

### Variables With Options
**ActivityDate**  
The date this content item was published
  
`format`

    the format of the date according to PHP [date() function](http://php.net/date) 

    accepted values are a date format string, relative (returns time ago such as "5 seconds ago"), or gmdate (a GMT date in PHP date format 'Y-M-d h:i A')

    defaults to `n/j/y`

**ActivityExcerpt**  
A trimmed version of `{ActivityBody}`

`length`

    the length of the excerpt to use
    
    accepted values are any integer
    
    defaults to 600
    
`cleaned`

    whether or not to remove all tags except `<span><em><strong><a><u><i><b>`
    
    accepted values are true and false
    
    defaults to false
    
`considerHtml`

    whether or not to include HTML tags as page of the length
    
    accepted values are true and false
    
    defaults to true
    
`ending`

    what to use at the end of the content if it is cut short
    
    accepted values are any string
    
    defaults to "..."

**ActivityPhotoUrl**  
A cover image for this content or a default one if there is no cover set

`size`

    the size of the photo to load
    
    accepted sizes are "thumbnail", "small", "medium", "large", "original"
    
    defaults to "thumbnail"
    
### If Statements
`if:ActivityHasActivityPhoto`

    check to see if the content item has a photo or not
    
`if:ActivityIs[%type]`
    
    check to see if a certain activity list item is of a certain type

    supported types: Audio, Blog, Event, Photo, PhotoAlbum, Status, StoreItem, Video

`if:ReadMore`

    check if the current excerpt is trimmed to something less than its total length

## Blog
    {page:BlogView}
        {module:BlogView}
            {block:BlogPost}
                <h2>{BlogPostTitle}</h2>
                {BlogPostBody}
            {/block:BlogPost}
        {/module:BlogView}
    {/page:BlogView}

### page:BlogList
`/blog` - This page should show a listing of blog posts

### page:BlogView
`/blog/[%id]` - This page should show the content of an individual blog post. If the post has a category, the URL would automatically change to `/blog/[%category]/[%id]`

### page:BlogCategoryView
`/blog/[%category]` - This page should show a listing of blog posts by category (`{module:BlogList}` will filter by the right category automatically on this page)

### module:BlogList
This module lists blog posts by an account. Blog posts are returned by the date they were published.

**Module Options**  
`direction`

    the direction in which to list the blog posts

    accepted values are asc or desc

    defaults to desc

`limit`

    the amount of blogs to list per page
    
    accepted values are any integer

    defaults to 5

`offset`

    skips X number of items based on the ordering, it is automatically adjusted when paging
    
    accepted values are any integer
    
    defaults to 0
        
`sticky`

    whether or not to include sticky posts

    defaults to showing both

    accepted values are true (to show only sticky posts) or false (to show only non-sticky posts)

`ignoreSticky`

    whether or not sticky blog posts should be on top of the listing

    accepted values are true or false

    defaults to false

`exclusive`

    whether or not to include exclusive posts

    defaults to showing both

    accepted values are true (to show only exclusive posts) or false (to show only non-exclusive posts)

`category`

    a category to filter the blog listing by
    
    accepted values are any string
    
    defaults to none (unless there is a category in the URL, then it defaults to that)

`ignoreCategory`

    whether or not to ignore a category if it shows up in the URL

    accepted values true and false

    defaults to false
    
`tag`

    a tag to filter the blog listing by

    accepted values are any string

    defaults to none
    
`includeFanContent`

    normally content submitted by fans is for module:FansiteContentList, but this allows it to be included here

    accepted values are true and false

    defaults to false

`accountId`

    a comma separated list of the IDs of the accounts to limit the results to
    
    accepted values are IDs of any children accounts of the current account (see variable {ChildAccountIDs})

    defaults to none (i.e. only the current account)

`paging` *(advanced)*

    define how many items are on this page
    
    if you are using multiple modules with both `limit`s and `offset`s then you need to explicitly set how many items are on the current page, or pagination will return unexpected results
    
    accepted values are any integer

### module:BlogView
This module will load the content for a single blog post. When on `{page:BlogView}` it will automatically grab the right data from the URL.

**Module Options**  
`fanSubmittedIsExclusive`

    whether or not to consider content submitted by fans to a fansite as exclusive or not by default

    accepted values are true and false

    defaults to true

### block:BlogPost
This block exposes various content available for a blog post object

**Variables**

AuthorName  
:   the author's full name

AuthorUsername  
:   the author's Fullscreen Direct username

AuthorUrl  
:   the URL to the public facing user page for this author

AuthorFansiteUrl  
:   the URL to the public facing user page for this author on the account's fansite

AuthorPhotoUrl  
:   url to a thumbnail sized, 130x130px user photo

BlogPostAccountId  
:    the ID of the account that this blog post belongs to

BlogPostTitle  
:   the title of the blog post

BlogPostCategory  
:   the category of the blog post

BlogPostId  
:   the blog post's ID

BlogPostUrl  
:   the permalink to the blog post's individual page

BlogPostShortUrl  
:   a short URL for the blog post

LikeCount  
:   the number of likes for the blog post

BlogPostBody  
:   the main content for the blog post

BlogPostExcerpt  
:   a 1500 character beginning of the blog post

BlogPostShortExcerpt  
:   a 400 character beginning of the blog post

BlogPostShortExcerptCleaned  
:   first 400 characters of blog post with all `<span><em><strong><a><u><i><b>` tags stripped

BlogPostShortExcerptStripped  
:   first 400 characters of blog post with all html tags stripped

BlogPostPhotoUrl  
:   the URL of the first `<img>` in the blog post (see `if:BlogPostHasPhoto`)

PreviousBlogPostId, NextBlogPostId  
:   the ID of the blog post previous/next to the current one if it exists

PreviousBlogPostTitle, NextBlogPostTitle  
:   the title of the blog post previous/next to the current one if it exists

PreviousBlogPostUrl, NextBlogPostUrl  
:   the permalink of the blog post previous/next to the current one if it exists

### Variables With Options
**PublishedDate**  
The date this blog post was published
  
`format`

    the format of the date according to PHP [date() function](http://php.net/date) 

    accepted values are a date format string, relative (returns time ago such as "5 seconds ago"), or gmdate (a GMT date in PHP date format 'Y-M-d h:i A')

    defaults to `n/j/y`
    
`shorttime`

    whether or not to show the units of time in a contracted manner (i.e. secs vs seconds) when using format="relative"

    accepted values are true or false
    
    defaults to false

### If Statements
`if:BlogPostHasPhoto`

    check if a blog post has at least one photo (`<img>` tag) in it
    
`if:ReadMore`

    check if the current excerpt is trimmed to something less than its total length
    
`if:BlogPostIsInCategory`

    checks to see if the blog post is in a specific category or not
    
    pass it a "category" parameter with a comma separated list of categories to check
    
`if:ContentIsFanSubmitted`

    checks to see if the blog post is fan submitted
    
## Audio
### page:AudioList
`/audio` - This page should show a listing of audio content such as audio tracks or audio playlists

### page:AudioView
`/audio/[%id]` - This page should show content for an individual audio track

### module:AudioList
This module lists audio tracks by an account. Audio is listed by the date it was created  (unless an `audioPlaylistId` is specified, then they are listed by their order in that playlist).

**Module Options**  
`audioPlaylistId`

    a audio playlist ID to filter the audio tracks by
    
    accepted values are any ID of an audio playlist that belongs to the same account

    defaults to none

`limit`

    the amount of audio tracks to list per page
    
    accepted values are any integer

    defaults to 10

`offset`

    skips X number of items based on the ordering, it is automatically adjusted when paging

    accepted values are any integer

    defaults to 0

`sticky`

    whether or not to just return the sticky audio track

    accepted values are true or false

    defaults to false

`direction`

    the direction in which to list the audio
    
    accepted values are asc or desc

    defaults to asc
    
`accountId`

    a comma separated list of the IDs of the accounts to limit the results to

    accepted values are IDs of any children accounts of the current account (see variable {ChildAccountIDs})

    defaults to none (i.e. only the current account)

### module:AudioView
This module will load the content for a single audio track. When on `{page:AudioView}` it will automatically grab the right data from the URL.

**Module Options**  
`audioId`

    an ID for which audio track to show
    
    accepted values are any audio ID that belongs to the same account
    
    defaults to none or the ID from the URL if on {page:AudioView}
    
`fanSubmittedIsExclusive`

    whether or not to consider content submitted by fans to a fansite as exclusive or not by default

    accepted values are true and false

    defaults to true

### block:AudioView
This block exposes various content for an audio object

AuthorName  
:   the name of the user that created this audio track

AuthorUsername  
:   the Fullscreen Direct username of the user that created this audio track

AuthorUrl  
:   the URL to the public facing user page for this author

AuthorFansiteUrl  
:   the URL to the public facing user page for this author on the account's fansite

AuthorPhotoUrl  
:   url to a thumbnail sized, 130x130px user photo

AudioAccountId  
:    the ID of the account that created this audio

AudioUrl  
:   a permalink to the audio track's individual page

AudioShortUrl  
:   the short URL for this audio track

AudioTitle  
:   the title of the audio track

AudioJavaScriptEscapedTitle  
:   the title of the audio track for use within JavaScript

AudioId  
:   the ID of the audio track

LikeCount  
:   the number of likes for the audio track

AudioLyrics  
:   the lyrics for the audio track

AudioDescription  
:   the description for the audio track

AudioPrice  
:   the price for this audio track in USD

AudioStreamUrl
:   the URL to use when streaming the audio track

AudioCredits  
:   the credits for the audio object

AudioArtist  
:   the artist for the audio track if given, otherwise the name of the account this audio belongs to

AudioFreeDownloadQuality, AudioPaidDownloadQuaility  
:   a string representing the quality of the free/paid version of this audio track, one of the following values:

    128kb MP3, 320kb MP3, Original WAV, Original AIFF, Original WAV / AIFF

### Variables With Options
**AudioAddToCartLink**  
Creates a link that, when clicked, will add the audio track to the user's cart on Fullscreen Direct

`audioId` (*required*)

    the ID of the audio track you want to add to the user's cart

`text`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Add To Cart"

`closeTag`

    whether or not to close the <a> tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the <a> tag
    
    accepted values are any string
    
    defaults to none

**AudioFreeDownloadLink**  
Creates a link that, when clicked, will download the audio track (unless it requires a follow to download, in which a modal will first show up)

`audioId` (*required*)

    the ID of the audio track you want to add to the user's cart

`text`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Add To Cart"

`closeTag`

    whether or not to close the <a> tag
    
    accepted values are true or false
    
    defaults to true

`class`

    the class to assign to the <a> tag
    
    accepted values are any string
    
    defaults to none

**AudioRecordedDate**  
The date this audio was recorded on
    
format

    the format of the date according to PHP [date() function](http://php.net/date) 

    accepted values are a date format string, relative (returns time ago such as "5 seconds ago"), or gmdate (a GMT date in PHP date format 'Y-M-d h:i A')

    defaults to `n/j/y`

**AudioPhotoUrl**  
A cover image for this audio track or a default one if there is no cover set

`size`

    the size of the photo to load
    
    accepted sizes are "thumbnail", "small", "medium", "large", "original"
    
    defaults to "thumbnail"
    
### If Statements
`if:AudioHasLyrics`

    Check to see if the audio track has lyrics

`if:AudioCanBeDownloadedForFree`
    
    Check to see if the audio track can be downloaded for free

`if:AudioCanBeSold`

    Check to see if the audio track is being sold

`if:CartHasProducts` *(global, can be used anywhere)*

    checks to see if there are items in the cart

## Audio Playlists
### page:AudioPlaylistList
`/audio/playlists` - This page should show a listing of audio playlists

### page:AudioPlaylistView
`/audio/playlists/[%id]` - This page should show content for an individual audio playlist

### module:AudioPlaylistList
This module lists audio playlists by an account.

**Module Options**  
`sticky`

    whether or not to list only the sticky audio playlist

    accepted values are true or false

    defaults to false

`featuredFirst`

    whether or not to show the sticky audio playlist first in the listing
    
    accepted values are true or false
    
    defaults to false
    
`accountId`

    a comma separated list of the IDs of the accounts to limit the results to

    accepted values are IDs of any children accounts of the current account (see variable {ChildAccountIDs})

    defaults to none (i.e. only the current account)

### module:AudioPlaylistView
This module will load the content for a single audio playlist. When on {page:AudioPlaylistView} it will automatically grab the right data from the URL.

**Module Options**  
`audioPlaylistId`

    an ID for which audio playlist to show
    
    accepted values are any audio playlist ID that belongs to the same account
    
    defaults to none or the ID from the URL if on {page:AudioPlaylistView}

### block:AudioPlaylistView
This block exposes various content for an audio playlist object

AudioPlaylistDescription  
:   the description for the audio playlist

AudioPlaylistId  
:   the ID for the audio playlist

AudioPlaylistUrl  
:   a permalink for the playlist's individual page

AudioPlaylistTitle  
:   the title of the audio playlist

AudioPlaylistAudioCount  
:   the amount of audio tracks in this playlist

AudioPlaylistPrice  
:   the price of this playlist if it is being sold

AudioPlaylistLabel  
:   the record label for the audio playlist

LikeCount  
:   the number of likes for the audio playlist

AudioPlaylistPrice  
:   the price for the playlist (if there is a pre-order currently active for the playlist, it'll use that price instead)

CreatedByPhotoUrl, ModifiedByPhotoUrl
:   a URL to the photo for who created/modified the audio playlist to be used in an `<img />` tag

CreatedByName, ModifiedByName
:   the name of the user who created/modified the audio playlist

AudioPlaylistFreeDownloadQuality, AudioPlaylistPaidDownloadQuality  
:   a string representing the quality of the free/paid version of this audio playlist, one of the following values:

    128kb MP3, 320kb MP3, Original WAV, Original AIFF, Original WAV / AIFF

### Variables With Options
**AudioPlaylistPhotoUrl**  
A cover image for this audio playlist or a default one if there is no cover set

`size`

    the size of the photo to load
    
    accepted sizes are "thumbnail", "small", "medium", "large", "original"
    
    defaults to "thumbnail"

**AudioPlaylistReleaseDate** 
The date the audio playlist was released on
    
`format`

    the format of the date according to PHP [date() function](http://php.net/date) 

    accepted values are a date format string, relative (returns time ago such as "5 seconds ago"), or gmdate (a GMT date in PHP date format 'Y-M-d h:i A')

    defaults to `n/j/y`

**AudioPlaylistAddToCartLink**  
Creates a link that, when clicked, will add the audio track to the user's cart on Fullscreen Direct

`audioPlaylistId` (*required*)

    the ID of the audio playlist you want to add to the user's cart

`text`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Add To Cart"

`closeTag`

    whether or not to close the <a> tag

    accepted values are true or false

    defaults to true

`class`

    the class to assign to the <a> tag

    accepted values are any string

    defaults to none

**AudioPlaylistFreeDownloadLink**  
Creates a link that, when clicked, will download the audio track (unless it requires a follow to download, in which a modal will first show up)

`audioPlaylistId` (*required*)

    the ID of the audio track you want to add to the user's cart

`text`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Add To Cart"

`closeTag`

    whether or not to close the <a> tag

    accepted values are true or false

    defaults to true

`class`

    the class to assign to the <a> tag

    accepted values are any string

    defaults to none

### If Statements
`if:AudioPlaylistCanBeDownloadedForFree`

    check to see if the audio playlist can be downloaded for free

`if:AudioPlaylistCanBeSold`

    check to see if the audio playlist is being sold
    
`if:AudioPlaylistCanNamePrice`

    check to see if the playlist is being sold and fans can name a price for it or not
    
`if:AudioPlaylistHasLabel`

    check to see if the audio playlist has a label or not
    
`if:AudioPlaylistHasReleaseDate`

    check to see if the audio playlist has a release date or not
    
`if:AudioPlaylistHasThumbnail`

    check to see if the audio playlist has a photo or not

`if:CartHasProducts` *(global, can be used anywhere)*

    checks to see if there are items in the cart

## Videos
### page:VideoList
`/videos` - This page should show a listing of video content such as videos or video playlists

### page:VideoView
`/videos/[%id]` - This page should show the content for an individual video

### VideoList
This module lists videos by an account. Videos are returned by the date they were created (unless a `videoPlaylistId` is specified, then they are listed by their order in that playlist).

**Module Options**  
`videoPlaylistId`

    a video playlist ID to filter the videos by

    accepted values are any ID of a video playlist that belongs to the same account

    defaults to none
    
`limit`

    the amount of videos to list per page

    accepted values are any integer

    defaults to 50

`offset`

    skips X number of items based on the ordering, it is automatically adjusted when paging

    accepted values are any integer

    defaults to 0

`direction`

    the direction in which to list the videos

    accepted values are asc or desc

    defaults to asc
    
`sticky`

    whether or not to include sticky videos

    defaults to showing both

    accepted values are true (to show only sticky posts) or false (to show only non-sticky posts)
    
`accountId`

    a comma separated list of the IDs of the accounts to limit the results to

    accepted values are IDs of any children accounts of the current account (see variable {ChildAccountIDs})

    defaults to none (i.e. only the current account)

### VideoView
This module will load the content for a single video. When on `{page:VideoView}` it will automatically grab the right data from the URL.

**Module Options**  
`videoId`

    an ID for which video to show

    accepted values are any video ID that belongs to the same account

    defaults to none or the ID from the URL if on {page:VideoView}

`fanSubmittedIsExclusive`

    whether or not to consider content submitted by fans to a fansite as exclusive or not

    accepted values are true and false

    defaults to true

### block:VideoView
AuthorName  
:   the name of the user that created this audio track

AuthorUsername  
:   the Fullscreen Direct username of the user that created this audio track

AuthorUrl  
:   the URL to the public facing user page for this author

AuthorFansiteUrl  
:   the URL to the public facing user page for this author on the account's fansite

AuthorPhotoUrl  
:   url to a thumbnail sized, 130x130px user photo

VideoAccountId  
:    the ID of the account that posted the video

VideoCreatorId  
:   the ID of the user who created the video

VideoUrl  
:   a permalink to the video's individual page

VideoJavaScriptEscapedTitle  
:   the title of the video escaped so that it is able to be used with JavaScript

VideoId  
:   the ID of the video

LikeCount  
:   the number of likes for the video

VideoShortUrl  
:   a short URL for the video

VideoDescription  
:   the description of the video

VideoJavaScriptEscapedDescription  
:   the description of the video escaped to be used in JavaScript

VideoEmbedCode  
:   the embed code for the given video

VideoJavaScriptEncodedEmbedCode  
:   the embed code encoded for use in a URL

VideoJavaScriptEscapedEmbedCode  
:   the embed code escaped to be used in JavaScript

### Variables With Options
**VideoPhotoUrl**  
A cover image for this video or a default one if there is no cover set

`size`

    the size of the photo to load
    
    accepted sizes are "thumbnail", "small", "medium", "large", "original"
    
    defaults to "thumbnail"

### If Statements
`if:AudioPlaylistHasThumbnail`

    Check to see if the audio playlist has a photo or not

## Video Playlists
### page:VideoPlaylistList
`/videos/playlists` - This page should show a listing of video playlists

### page:VideoPlaylistView
`/videos/playlists/[%id]` - This page should show content for an individual video playlist

### VideoPlaylistList
This module lists video playlists by an account.

**Module Options**  
`limit`

    the amount of video playlists to list per page

    accepted values are any integer

    defaults to 50

`offset`

    skips X number of items based on the ordering, it is automatically adjusted when paging

    accepted values are any integer

    defaults to 0
    
`direction`

    the direction in which to list the videos

    accepted values are asc or desc

    defaults to asc

`accountId`

    a comma separated list of the IDs of the accounts to limit the results to

    accepted values are IDs of any children accounts of the current account (see variable {ChildAccountIDs})

    defaults to none (i.e. only the current account)

### VideoPlaylistView
This module will load the content for a single vide playlist. When on {page:VideoPlaylistView} it will automatically grab the right data from the URL.

### block:VideoPlaylistView
VideoPlaylistDescription  
:   the description for the playlist

VideoPlaylistId  
:   the ID for the video playlist

VideoPlaylistUrl  
:   a permalink to the video playlist's individual page

VideoPlaylistTitle  
:   the title for the video playlist

LikeCount  
:   the number of likes for the video playlist

VideoPlaylistVideoCount  
:   the amount of videos in this playlist

CreatedByPhotoUrl, ModifiedByPhotoUrl  
:   a URL to the thumbnail photo for the user who created/modified the video playlist to be used in an `<img src="" />` tag

CreatedByName, ModifiedByName  
:   the name of the user who created/modified the video playlist

### Variables With Options
**VideoPlaylistPhotoUrl**  
A cover image for this video video or a default one if there is no cover set

`size`

    the size of the photo to load
    
    accepted sizes are "thumbnail", "small", "medium", "large", "original"
    
    defaults to "thumbnail"
    
### If Statements
`if:VideoPlaylistHasThumbnail`

    check to see if the video playlist has a photo or not

## Photos
### page:PhotoList
`/photos` - This page should show a listing of all your account's photos and/or photo albums

### page:PhotoView
`/photos/[%id]` - This page should show content for an individual photo

### module:PhotoList
This module lists photos by an account. Photos is listed by the date it was created (unless an audioPlaylistId is specified, then they are listed by their order in that playlist).

**Modules Options**  
`albumId`

    a photo album ID to filter the photos by
    
    accepted values are any ID of an photo album that belongs to the same account

    defaults to none

`limit`

    the amount of photos to list per page

    accepted values are any integer

    defaults to 10

`offset`

    skips X number of items based on the ordering, it is automatically adjusted when paging

    accepted values are any integer

    defaults to 0

`direction`

    the direction in which to list the videos

    accepted values are asc or desc

    defaults to desc

`accountId`

    a comma separated list of the IDs of the accounts to limit the results to

    accepted values are IDs of any children accounts of the current account (see variable {ChildAccountIDs})

    defaults to none (i.e. only the current account)

`includeFanContent`

    normally content submitted by fans is for module:FansiteContentList, but this allows it to be included here

    accepted values are true and false

    defaults to false

`accountContent`

    whether or not to show photos created by admins of the account
    
    accepted values are true or false

    defaults to true

`exclusive`

    whether or not to show exclusive content

    accepted values are true or false

    defaults to showing both in the case that the user is logged in and authorized to see it
    
`userId`

    the user to limit the photos to
    
    accepted values are an ID of an admin if accountContent is enabled or an ID of a fan is includeFanContent is enabled

    defaults to none (i.e. all fans and / or all admins)

`showPrivate`

    whether or not to include private photos in the listing
    
    accepted values are true or false
    
    defaults to false

### module:PhotoView
This module will load the content for a single photo. When on {page:PhotoView} it will automatically grab the right data from the URL.

**Module Options**  
`photoId`

    an ID for which photo to show

    accepted values are any photo ID that belongs to the same account

    defaults to none or the ID from the URL if on {page:PhotoView}

`fanSubmittedIsExclusive`

    whether or not to consider content submitted by fans to a fansite as exclusive or not

    accepted values are true and false

    defaults to true

### block:PhotoView
AuthorName  
:   the name of the user that created this audio track

AuthorUsername  
:   the Fullscreen Direct username of the user that created this audio track

AuthorUrl  
:   the URL to the public facing user page for this author

AuthorFansiteUrl  
:   the URL to the public facing user page for this author on the account's fansite

AuthorPhotoUrl  
:   url to a thumbnail sized, 130x130px user photo

PhotoAccountId  
:    the ID of the account that created the photo

PhotoCreatorId  
:   the ID of the user who created the photo

PhotoId  
:   the ID of the photo

PhotoUrl  
:   the permalink to this photo's individual page

PhotoShortUrl  
:   a short URL for the photo

LikeCount  
:   the number of likes for the photo

PhotoTitle  
:   the title of the photo

PhotoDescription  
:   the description of the photo

Source-Thumb  
:   a squared thumbnail photo link with the dimensions of 130x130px

Source-Small  
:   a link to a photo with max dimensions of 250x250px

Source-Medium  
:   a link to a photo with max dimensions of 500x500px

Source-Large  
:   a link to a photo with max dimensions of 800x800px

Source-Original  
:   a link to the originally uploaded photo

PreviousPhotoId, NextPhotoId  
:   the ID of the photo previous/next to the current one if it exists

PreviousPhotoTitle, NextPhotoTitle  
:   the title of the photo previous/next to the current one if it exists

PreviousPhotoUrl, NextPhotoUrl  
:   the permalink of the photo previous/next to the current one if it exists

### Variables With Options
**PhotoAddToCartLink**  
Creates a link that, when clicked, will add the photo to the user's cart

`photoId` (*required*)

    the ID of the photo you want to add to the user's cart

`text`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Add To Cart"

`closeTag`

    whether or not to close the <a> tag

    accepted values are true or false

    defaults to true

`class`

    the class to assign to the <a> tag

    accepted values are any string

    defaults to none

### If Statements
`if:PhotoCanBeSold`

    checks to see if a photo can be sold or not

## Photo Albums
### page:PhotoAlbumList
`/photos/albums` - This page should show a listing of photo albums

### page:PhotoAlbumView
`/photos/albums/[%id]` - This page should show the content for an individual photo album

### module:PhotoAlbumList
This module lists photo albums by an account.

**Module Options**  
`limit`

    the amount of photo albums to list per page

    accepted values are any integer

    defaults to 10

`offset`

    skips X number of items based on the ordering, it is automatically adjusted when paging

    accepted values are any integer

    defaults to 0

`direction`

    the direction in which to list the photo albums

    accepted values are asc or desc

    defaults to desc

`accountId`

    a comma separated list of the IDs of the accounts to limit the results to

    accepted values are IDs of any children accounts of the current account (see variable {ChildAccountIDs})

    defaults to none (i.e. only the current account)

### module:PhotoAlbumView
This module will load the content for a single photo album. When on {page:PhotoAlbumView} it will automatically grab the right data from the URL.

**Module Options**  
`albumId`

    an ID for which photo album to show

    accepted values are any photo album ID that belongs to the same account

    defaults to none or the ID from the URL if on {page:PhotoView}

### block:PhotoAlbumView
PhotoAlbumId  
:   the ID of the photo album

PhotoAlbumAccountId  
:    the ID of the account that created the photo album

PhotoAlbumDescription  
:   the description of the photo album

PhotoAlbumUrl  
:   a permalink to the photo album's individual page

PhotoAlbumShortUrl  
:   a show URL for this photo album

LikeCount  
:   the number of likes for the photo album

PhotoAlbumPhotoCount  
:   the number of photos in this photo album

CreatedByPhotoUrl, ModifiedByPhotoUrl  
:   a URL to the photo for who created/modified the album to be used in an `<img src="" />` tag

CreatedByName, ModifiedByName  
:   the name of the user who created/modified the photo album

### Variables With Options
**PhotoAlbumPhotoUrl**  
A cover image for this photo album or a default one if there is no cover set

`size`

    the size of the photo to load
    
    accepted sizes are "thumbnail", "small", "medium", "large", "original"
    
    defaults to "thumbnail"

## Events
This section contains various information for how to show data for events on your website. Keep in mind SEO and other metadata when creating this page, as it can help with search results. For Google specifically, you have a [few options](http://googlewebmastercentral.blogspot.com/2014/03/musical-artists-your-official-tour.html) for [metadata](https://support.google.com/webmasters/answer/164506?hl=en). The easiest method is probably just to have your markup conform to [schema.org's Event schema](http://schema.org/Event).

### page:EventPastList
`/events/past` - This page should show a listing of events that have already occurred

### page:EventUpcomingList
`/events`, `/events/upcoming` - This page should show a listing of events that have not yet occurred

### page:EventView
`/events/[%id]` - This page should show information for an individual event

### module:EventList
This module lists events by an account. Events are returned by the date they occur.

**Module Options**  
`limit`

    the amount of events to list per page

    accepted values are any integer

    defaults to 50

`offset`

    skips X number of items based on the ordering, it is automatically adjusted when paging

    accepted values are any integer

    defaults to 0

`direction`

    the direction in which to list the events

    accepted values are asc or desc

    defaults to asc

`accountId`

    a comma separated list of the IDs of the accounts to limit the results to

    accepted values are IDs of any children accounts of the current account (see variable {ChildAccountIDs})

    defaults to none (i.e. only the current account)

`sticky`

    whether or not to include sticky events

    defaults to showing both

    accepted values are true (to show only sticky events) or false (to show only non-sticky events)

`upcoming`

    whether or not to show upcoming events

    accepted values are true or false

    defaults to true

`past`

    whether or not to include past events

    accepted values are true or false

    defaults to false

`userId`

    a userId to limit the events to those this user has RSVPed to

    accepts the ID of any user that is a fan of the account

    defaults to none
    
`attending`

    when used in combination with userId, a way to filter the attending status of those events

    accepted values are "yes", "no", and "maybe"

    defaults to none

`useLiteralSearch`

    when used with page:EventSearch this will determine if the search phrase must match exactly for a result to count

    accepted values are true or false

    defaults to false

`useSearchFields`

    when used with page:EventSearch this will change the fields used in the search results and their weighted priority (earlier items in the list count more strongly against a match)

    accepted values are any CSV combination of "title", "tags", and "text" (i.e. "title,tags")

    defaults to "title,tags,text"


### module:EventView
This module will load the content for an event. When on {page:EventView} it will automatically grab the right data from the URL.

**Module Options**  
`eventId`

    an ID for which event to show

    accepted values are any event ID that belongs to the same account

    defaults to none or the ID from the URL if on {page:EventView}

### block:EventView
EventAccountId  
:   the ID of the account that created the event

EventTitle  
:   the title of the event

EventDescription  
:   the description of the event

EventAges  
:   will return either "Any age" or "[%Age]+" (meaning this age and up)

EventContestId  
:   the ID for the contest for this event (if it has one)

EventId  
:   the ID of the event

EventLikeCount  
:   the number of likes for the event

EventAttendingCount  
:   the number of users that have RSVPed "Yes"

EventMaybeAttendingCount  
:   the number of users that have RSVPed "Maybe"

EventNotAttendingCount  
:   the number of users that have RSVPed "No"

EventUrl  
:   a permalink to the event's individual page

EventShortUrl  
:   a short URL for the event

EventMapUrl  
:   a URL to an embeddable map for the event

EventLocation  
:   a string constructed based on the information available for the location. if in the USA, it will return "city, state", otherwise it will return "city, state, country"

EventStreetAddress  
:   the street address of the event's location

EventCity  
:   the city in which the event is taking place

EventState  
:   the state in which the event is taking place

EventCountry  
:   the country in which the event is taking place

EventLocationLatitude  
:   the latitude of the location for the event

EventLocationLongitude  
:   the longitude of the location for the event

EventPrice  
:   the event for the price if it has one

TicketsBuyLink  
:   a link to where tickets for this event can be purchased, will return a link to the presale tickets if it's a presale or the normal tickets if not

VenueName  
:   the name of the venue where this event is taking place

VenueWebsiteUrl  
:   the URL to the venue where the event is taking place

### Variables With Options
**EventPhotoUrl**  
A cover image for this event or a default one if there is no cover set

`size`

    the size of the photo to load
    
    accepted sizes are "thumbnail", "small", "medium", "large", "original"
    
    defaults to "thumbnail"

**EventStartDate**  
The date and time this event starts

`format`

    the format of the date according to PHP [date() function](http://php.net/date) 

    accepted values are a date format string, relative (returns time ago such as "5 seconds ago"), or gmdate (a GMT date in PHP date format 'Y-m-d H:i:s')

    defaults to `n/j/y`

**EventEndDate**  
The date and time this event end

`format`

    the format of the date according to PHP [date() function](http://php.net/date) 

    accepted values are a date format string, relative (returns time ago such as "5 seconds ago"), or gmdate (a GMT date in PHP date format 'Y-m-d H:i:s')

    defaults to `n/j/y`

### If Statements
`if:EventHasContest`

    check to see if an event has a contest or not

`if:EventHasEnded`

    check if an event has finished or not

`if:EventHasMinimumAge`

    check if an event has a required minimum age (any age greater than zero).

`if:EventHasPhoto`

    check to see if an has a cover photo or not

`if:EventHasPrice`

    check if an event has a price of more than $0.00

`if:EventHasTicketsBuyLink`

    check if the event has a ticket buy link attached, if the event has a presale and the presale hasn't started yet then this will return false

`if:EventPresaleIsActive`

    check to see if the presale for an event is active
    
`if:UserEventAttendingStatus`

    check to see if the logged in user is attending the event
    
    requires a "status" paramers of "yes", "no", or "maybe"

`if:HasPastEvents` *(global, can be run from anywhere)*

    check if past events exist for current account

`if:HasUpcomingEvents` *(global, can be run from anywhere)*

    check if upcoming events exist for the current account

## Statuses
### page:StatusList
`/statuses` - This page should show a listing of statuses posted by your account

### page:StatusView
`/statuses/[%id]` - This page should show an individual status

### module:StatusList
This module lists statuses by an account. Statuses are returned by the date they were created.

**Module Options**  
`limit`

    the amount of statuses to list per page

    accepted values are any integer

    defaults to 10

`offset`

    skips X number of items based on the ordering, it is automatically adjusted when paging

    accepted values are any integer

    defaults to 0

`direction`

    the direction in which to list the statuses

    accepted values are asc or desc

    defaults to desc

`accountId`

    a comma separated list of the IDs of the accounts to limit the results to

    accepted values are IDs of any children accounts of the current account (see variable {ChildAccountIDs})

    defaults to none (i.e. only the current account)

`fanContent`

    normally content submitted by fans is for module:FansiteContentList, but this allows it to be included here

    accepted values are true and false

    defaults to false

`accountContent`

    whether or not to show photos created by admins of the account

    accepted values are true or false

    defaults to true

### module:StatusView
This module will load the content for an status. When on {page:StatusView} it will automatically grab the right data from the URL.

**Module Options**  
`statusId`

    an ID for which status to show

    accepted values are any status ID that belongs to the same account

    defaults to none or the ID from the URL if on {page:StatusView}
    
`fanSubmittedIsExclusive`

    whether or not to consider content submitted by fans to a fansite as exclusive or not

    accepted values are true and false

    defaults to true

### block:StatusPost

AuthorName  
:   the author of the status

AuthorPhotoUrl  
:   a 130x130 pixel image of the author of the photo or a placeholder if the photo doesn't exist

StatusId  
:   the ID of the status was posted

StatusAccountId  
:    the ID of the account that posted this status

StatusUrl
:   a permalink to an individual status post page

StatusShortUrl  
:   a short URL for the status

LikeCount
:   the amount of likes for this status

StatusText  
:   the actual text of the status post

StatusTextWithLinks  
:   the actual text of the status post with links converted into HTML `<a>` tags

## Store Items
### page:StoreItemList
`/store` - This page should show a listing of store items from your account

### page:StoreItemView
`/store/[%id]` - This page should show content for an individual store item

### page:StoreCategoryView
`/store/[%category]` - This page should show a listing of store items by category ({module:StoreItemList} will filter by the right category automatically on this page)

### module:StoreItemList
This module lists store items by an account. Store items are listed by the date it was created.

**Module Options**  
`limit`

    the amount of store items to list per page

    accepted values are any integer

    defaults to 20

`offset`

    skips X number of items based on the ordering, it is automatically adjusted when paging

    accepted values are any integer

    defaults to 0

`direction`

    the direction in which to list the store items

    accepted values are asc or desc

    defaults to desc
   
`category`

    the category of store items to list

    accepted values are any store category

    defaults to none
    
`sticky`

    whether to limit this to sticky items or not

    accepted values are true or false

    defaults to showing both
   
   
`accountId`

    a comma separated list of the IDs of the accounts to limit the results to

    accepted values are IDs of any children accounts of the current account (see variable {ChildAccountIDs})

    defaults to none (i.e. only the current account)

`type`

    the type of store items to include

    accepted values are any comma separated string of "digital", "physical", "bundle", and "experience"
    
    defaults to all types

`onSale`

    include or exclude items that are on sale; true is only sale items, false is only items that are not on sale

    accepted values are true and false
    
    defaults to all items
    
`exclusive`

    include or exclude items that are exclusive; be default exclusive items will only be included for fans

    accepted values are true and false
    
    defaults to null
    
`status`

    the status of the store items to include

    accepted values are any comma separated string of the following constants: listed, coming_soon, sold_out
    
    defaults to all "listed,coming_soon,sold_out"

**Module Variables**  
`{StoreCategory}`

    the category slug used; useful for {page:StoreCategoryView}

    returns string or null

### module:StoreItemView
This module will load the content for a single audio track. When on {page:StoreItemView} it will automatically grab the right data from the URL.

**Module Options**  
`storeItemId`

    an ID for which store item to show

    accepted values are any store item ID that belongs to the same account

    defaults to none or the ID from the URL if on {page:StoreItemView}
    
`ignoreStatus`

    whether or not to ignore the status of the store item to be shown

    accepted values are true and false

    defaults to false
    
### block:StoreItemView

StoreItemAccountId  
:    the ID of the account that created the store item

StoreItemUrl  
:   the URL for the store item

StoreItemTitle  
:   the title of the store item

StoreItemDescription  
:   the description of the store item

StoreItemCategory  
:   the category of the store item

LikeCount  
:   the number of likes for this store item

StoreItemPrice  
:   the price of the store item with any active sales applied *(if there is a pre-order currently active for the store item, it'll use that price instead)*

StoreItemOriginalPrice  
:   the original price of the store item (i.e. no sales applied) *(if there is a pre-order currently active for the store item, it'll use that price instead)*

StoreItemPrice  
:   the price of the store item with any sales applied

StoreItemId  
:   the ID for the store item

StoreItemCreatedById
:   the ID of the person who created this store item

StoreItemPhotoAlbumId  
:   the ID of the photo album that holds the photos for this store item *(this can be used with `module:PhotoList` to easily list store item photos)*

### Variables With Options
**StoreItemPhotoUrl**  
A cover image for this store item or a default one if there is no cover set

`size`

    the size of the photo to load
    
    accepted sizes are "thumbnail", "small", "medium", "large", "original"
    
    defaults to "thumbnail"
    
**StoreItemPrice**  
The price of the store item

`currency`

    the currency to show the price in
    
    accepted values are "gbp", "usd", and "eur"
    
    defaults to "usd"

`includeSymbol`

    whether or not to include the symbol of the currency
    
    accepted values are true and false
    
    defaults to false
    
**StoreItemAddToCartLink**  
Creates a link that, when clicked, will add the store item to the user's cart

For instance,  
```
{StoreItemAddToCartLink storeItemId="{StoreItemId}" text="Buy Now"}
```  
would change to  
```
<a href="javascript:void(0);" onclick="<magical_js>">Buy Now</a>
```

`storeItemId` (*required*)

    the ID of the store item you want to add to the user's cart

`sku`

    a SKU to auto-select in the cart

`text`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Add To Cart"

`preorderText`

    the text to be put inside the <a> tag when a pre-order is set up for the playlist

    accepted values are any string (HTML included)

    defaults to "Pre-order"

`preorderSoldOutText`

    the text to be put inside the <a> tag when a preorder is set up for the playlist and it has sold out

    accepted values are any string (HTML included)

    defaults to "Pre-order Sold Out"

`closeTag`

    whether or not to close the <a> tag

    accepted values are true or false

    defaults to true

`class`

    the class to assign to the <a> tag

    accepted values are any string

    defaults to none

`currency`

    a three character ISO code of the currency to use for this item, will fallback to "usd" if there are any items in the cart that don't support this same currency
    
    accepted values are "gbp", "usd", and "eur"
    
    defaults to "usd"

**StoreItemFreeDownloadLink**  
Creates a link that, when clicked, will download the store item is possible

`storeItemId` (*required*)

    the ID of the store item you want to add to the user's cart

`text`

    the text to be put inside the <a> tag

    accepted values are any string (HTML included)

    defaults to "Add To Cart"

`closeTag`

    whether or not to close the <a> tag

    accepted values are true or false

    defaults to true

`class`

    the class to assign to the <a> tag

    accepted values are any string

    defaults to none


### If Statements
`if:StoreItemCanBeDownloadedForFree`

    check to see if the store item can be downloaded for free

`if:StoreItemCanBeSold`

    check to see if the store item is being sold

    `currency`

        the currency to check if it can be sold in
    
        accepted values are usd, gbp, and eur
    
        defaults to usd

`if:StoreItemCanNamePrice`

    check to see if the store item supports naming a price

`if:StoreItemIsOnSale`

    check to see if the store item is on sale or not
    
`if:StoreItemIsPhysical`

    check to see if the store item is a physical good (instead of a digital good)

`if:StoreItemIsSoldOut`

    check to see if the store item is sold out or not

`if:CartHasProducts` *(global, can be used anywhere)*

    checks to see if there are items in the cart

### module:StoreItemOptionList
This module will load the various options/variations of a particular store item. 

**Module Options**
`storeItemId`

    an ID for which store item to show

    accepted values are any store item ID that belongs to the same account

`minimum`

    the minimum number of item options required for this module to iterate over each item option

    accepted values are numbers

    defaults to 1

### block:StoreItemOptionView
StoreItemOptionId

:	the id of the given option

StoreItemOptionName

:	the name

StoreItemOptionSku

:	the sku

StoreItemOptionUpc

:	the upc

StoreItemOptionAdditionalPrice

:	any additional price, if any, for selecting the given option/variation of the store item

StoreItemOptionWeight

:	the weight in ounces

### If Statements
`if:StoreItemOptionCanBeSold`

	check to see if the option can be sold because of various reasons, such as out of stock.


## Comments
When on one of the following pages, using the appropriate content's module view will automatically pull in the correct data (i.e. `{module:BlogView}` will be the correct data automatically on `{page:BlogCommentView}`)

### page:AudioCommentView
`/audio/[%audio_id]/comment/[%id]` - This page should show the content of an individual audio comment

### page:BlogCommentView
`/blog/[%blog_id]/comment/[%id]` - This page should show the content of an individual blog post comment

### page:EventCommentView
`/event/[%event_id]/comment/[%id]` - This page should show the content of an individual event comment

### page:PhotoCommentView
`/photos/[%photo_id]/comment/[%id]` - This page should show the content of an individual photo comment

### page:StatusCommentView
`/statuses/[%status_id]/comment/[%id]` - This page should show the content of an individual status comment

### page:VideoCommentView
`/videos/[%video_id]/comment/[%id]` - This page should show the content of an individual video comment

### page:StoreCommentView
`/store/[%store_item_id]/comment/[%id]` - This page should show the content of an individual store comment

### module:CommentList
A view for a listing of the most recent comments, it will use the content of the currently rendered item

**Module Options**  
`limit`

    the amount to limit the comments to

    accepted values are any integeer *(keep in mind that the {CommentLink} variable exists and so does the sbInlineLoadComments Advanced Functionality*
    
    defaults to 10
    
`parents`

    whether or not to get the parent comments for the individual comment

    accepted values are true or false

    defaults to false
    
`includeFirstLevelReplies`

    whether or not to include the first level of replies, may return more items than `limit` *(see if:CommentIsReply)*

    accepted values are true or false
    
    defaults to false
    
`userId``

    a user to filter the comments by
    
    accepted values are an user ID

    defaults to none

### module:CommentView
This module will load the content for a single comment. When on any of the above pages it will automatically grab the right data from the URL.

**Module Options**  
`contentId`

    the ID of the content to list the comments of

    accepted values are any ID of content belonging to the account of the comment

    defaults to the current modules item's ID in the theme engineComm

`contentSection`

    the type of the content to list the comments of

    accepted values are any of the {ContentType-[%type]} variables

    defaults to the current modules item's content type in the theme engine

### block:CommentView
CommentText  
:   the text of the comment

CommentHTML  
:   the text of the comment, formatted with `<br />`s for new lines, and `<a>` tags around links

CommentShortUrl  
:   a short URL to the comment

CommentAuthorUsername  
:   the comment author's username

CommentAuthorFansiteUrl  
:   the URL of this user on the current fansite

CommentAuthorUrl  
:   the URL to the main user page for the author

CommentAuthorPhotoUrl  
:   the URL to the thumbnail image for the user who submitted this comment

CommentUserId  
:   the ID of the user who posted the comment *Can be used with `block:UserView`

CommentId  
:   the ID for the comment

CommentContentType  
:   the type of content this comment was made on

CommentItemId  
:   the ID of the item the comment was written about

### If Statements
`if:CommentIsReply`

    check to see if a comment is a reply to another comment

## Contests

### page:ContestList
`/contests` - This page should show a listing of contests

### module:ContestView
This module loads the content for a single contest

### Options
**Module Options**  
`contestId`

    the ID of the contest to load

    accepted values are any contest that belongs to this account
    
    defaults to none

`eventId`

    the ID of the event the contests belong to

    accepted values are any events that belongs to the same account as the contests
    
    defaults to none

`userId`

    the ID of the user who has entered any contests, shows contests that user has entered (only works for currently logged in user)

    accepted values are any user ID
    
    defaults to none

`orderBy`

    which criteria to base the order of the loaded contests in

    accepted values are "created" and "endDateTime"
    
    defaults to "created"

`direction`

    the direction to load the contests in, along with orderBy

    accepted values are "asc" and "desc"
    
    defaults to "desc"

### block:ContestView
ContestId  
:   the ID of the contest

ContestTitle  
:   the title of the contest

ContestDescription  
:   the description of the contest

ContestParticipantCount  
:   the number of people who have entered the contest

ContestAllowedParticipantCount  
:   the number of allowed people for the contest

### If Statements
`if:ContestHasEnded`

    checks to see if the contest has ended
    
`if:ContestAllowsUnlimitedParticipants`

    checks to see if the contest allows any amount of participants

`if:UserHasEnteredContest`

    checks to see if the currently logged in user has entered the contest or not

`if:UserWonContent`

    checks to see if the currently logged in user has won the contest or not

`if:ContestWinnersWereSelected`

    checks to see if the winners for this contest were selected

## Orders
Items that users have ordered will only be shown using the logged in user's ID and can't be made public. An order has one or more transactions. Transactions can be used to show the actual items that were part of an order.

### module:OrderList
This module list orders for the logged in user

**Module Options**  
`limit`

    the amount of transactions to list per page

    accepted values are any integer

    defaults to 20
    
`refunded`

    whether or not to show refunded orders
    
    accepted values are true and false
    
    defaults to false

### module:TransactionList
This module lists transactions for the logged in user

**Module Options**  
`limit`

    the amount of transactions to list per page

    accepted values are any integer

    defaults to 20
    
`orderId`

    the order ID to limit the transactions to
    
    accepted values are any order ID of the logged in user
    
    defaults to none
    
### block:OrderView
OrderReceiptUrl  
:   a URL to the receipt for the order

OrderId  
:   the ID of the order

OrderTotal  
:   the total amount of money in USD spend on this order

OrderAmount  
:   the amount of money in USD spent on this order without shipping

OrderShippingAmount  
:   the amount of money in USD spent on shipping for this order

### block:TransactionView
TransactionOrderReceiptUrl  
:   a URL to the receipt for the order this transaction belongs to

TransactionId  
:   the ID of the transaction

TransactionOrderId  
:   the ID of the order this transaction belongs to

TransactionAmount  
:   the amount of money spent on this transaction in USD (doesn't include shipping)

TransactionTrackingNumber  
:   the tracking number for the item if it has been shipped

TransactionDownloadUrl  
:   a link to download the purchased item if it is digital

TransactionItemName  
:   the name of the item that was ordered

### Variables With Options
**OrderedDate**  
The GMT date the order was made

`format`

    the format of the date according to PHP [date() function](http://php.net/date) 

    accepted values are a date format string, relative (returns time ago such as "5 seconds ago")

    defaults to `n/j/y`

### If Statements
`if:OrderWasRefunded`

    checks to see if the order has been refunded or not

`if:OrderHasBeenShipped`

    checks to see if the order as a whole was shipped or not

`if:TransactionHasBeenShipped`

    checks to see if the transaction was shipped or not

`if:TransactionIsForDigitalItem`

    checks to see if the transaction was for a digital item or not

## Accounts
### module:ChildAccountList
A listing of accounts that are children of the current account

**Module Options**  
`type`

    the type of accounts to limit the results to

    accepted values are any comma separated combination account types you've created

    defaults to all types

### block:ChildAccountView
This block exposes information for a child account

ChildAccountName  
:   name of the child account

ChildAccountUrl  
:    the URL of the child account

ChildAccountId  
:    the ID of the child account

ChildAccountDescription  
:    the description of the child account

ChildAccountRelationship  
:   the type of child account this is

## Users
### page:UserView
`/fansite/users/[%id]` - This page should show an individual fan of your account

### page:UserList
`/fansite/users` - This page should show a list of fans of an account

### module:FanList
This module lists fans of an account.

**Module Options**  
`includeAdmins`

    whether or not to include admins in the listing
    
    accepted values are true or false

    defaults to false

`userGroupId`

    the ID of a user group to limit the fans by
    
    accepted values are any user group ID belonging to this account

    defaults to none

### module:UserView
This module will load the information for a single user

**Module Options**  
`userId`

    the ID of the user to show
    
    accepted values are the ID of any of the fans for the account

    defaults to the user ID in the URL

`loggedIn`

    whether or not to use the currently logged in user, will override userId if true

    accepted values are true and false

    defaults to false

### block:UserView
This block exposes information for an individual user

UserId  
:   the ID of the user

UserName  
:    the real name of the user

UserUsername  
:    the username of the user

UserFansitePostCount  
:   the number of posts the user has on the current account's fansite

UserFansiteLikeCount  
:   the number of likes the user has on the current account's fansite

UserFansiteCommentCount  
:   the number of comments the user has on the current account's fansite

UserEventAttendingCount  
:   the number of events the user is attending for the current account

UserUrl  
:   the URL to the user's page on Fullscreen Direct (i.e. `www.fullscreendirect.com/user/[%id]`)

UserFansiteUrl  
:   the URL to the user's pages on the current account's fansite (if they have one setup) (i.e. `www.fullscreendirect.com/[%account_url]/fansite/users/[%user_id]`)

### module:UserLinks
This module will list links a user has added to their profile

**Module Options**  
`userId`

    the ID of the user to show
    
    accepted values are the ID of any of the fans for the account

    defaults to the user ID in the URL

`loggedIn`

    whether or not to use the currently logged in user, will override userId if true

    accepted values are true and false

    defaults to false

### block:UserLink
This block exposes link information for an individual user's links

LinkUrl  
:   the URL for the link itself

LinkTitle  
:    the title of the link

LinkTitleSlug  
:    a slug for the link

### Variables With Options
**UserPhotoUrl**  
This users profile image

`size`

    the size of the photo to load
    
    accepted sizes are "thumbnail", "small", "medium", "large", "original"
    
    defaults to "thumbnail"

`defaultPhoto`

    the URL of a photo to use if one isn't set and you don't want to use our default photo
    
    accepted values are any image URL
    
    defaults to our default image
    
### If Statements
`if:UserHasFacebookConnected` *(global, can be used anywhere)*

    checks to see if the currently logged in user has Facebook connected

`if:UserHasTwitterConnected` *(global, can be used anywhere)*

    checks to see if the currently logged in user has Twitter connected

`if:UserIsAdmin` *(global, can be used anywhere)*

    checks to see if the user is logged in and an admin of the current account

`if:UserIsFollowing` *(global, can be used anywhere)*

    checks to see if the user is logged in and following the current account or not

`if:UserIsMe`

    checks to see if the current user is the logged in user
    
`if:UserBelongsToFansiteTier`

    checks to see if the user is logged in and belongs to a specific tier in a fansite. Possible tiers are 1, 2, and 3.

    `tier`

        the specific tier to check to see if the user belongs to, will override gt and lt
    
        accepted values are any integer
    
        defaults to none

    `gt`

        check if the tier is greater than a certain tier
    
        accepted values are any integer
    
        defaults to none

    `lt`

        check if the tier is less than a certain tier

        accepted values are any integer

        defaults to none

`if:UserCanViewContent`

    checks to see if a user can view the current content based on its exclusivity and any other relevant factors

`if:UserIsLoggedIn` *(global, can be used anywhere)*

    checks to see if there's a logged in user

`if:UserIsVerified` *(global, can be used anywhere)*

    checks to see if a user has verified their email address or not

# SBNav
SBNav is the little gray control box that appears in the bottom right corner of the screen. It allows users to, among other things, log in, follow and unfollow accounts, and manage their shopping cart. It is also the communication layer that our Advanced Functionality features employs.

If an account has white labeling enabled, SBNav may be removed from their account.

### Notes and Best practices
SBNav adds a few CSS rules to your `<html>` element, namely `position: relative` and a few `transition`s. These allow the page to animate to the left while SBNav slides open.

If you use `position: fixed` in your theme (sticky headers, etc), you may notice that the sliding animation of the page becomes unsightly. You can disable it by adding `html { position: initial !important; }` to your CSS.

# Advanced Functionality
Using JavaScript and SBNav we are able to expose some advanced functionality. The functionality allowed here is available using built in Fullscreen Direct modals, but these methods allow theme authors to have a little bit more control over how it looks and works in their theme. In general, advanced functionality is achieved by targeting SBNav via `postMessage` as follows:

    pm({ target: window.frames.sbnav, type: 'advancedFunctionalityType', data: { key: value } });

The postMessage JS library is already included for you. The `target` parameter must be exactly as shown above.

When a request such as the above is made, SBNav will act accordingly given the data provided and callback one of the following two bindings:

    pm.bind('advancedFunctionalityType', function(data) {
        // data will be a JavaScript object representing the state of the request
        // If data.error is defined, an error occurred during the request
    });

    pm.bind('sbError', function(data) {
        // This binding will handle generic errors (i.e. if SBNav receives a 500 or anything for any reason)
        // data.type will be the action it came from (i.e. 'advancedFunctionalityType')
    });
    
## postMessage Hooks
Some platform modals will return a message with extra data upon success. These can be used to reload stale data and provide a more app-like experience.

You can listen for these events with a `pm.bind` call. For example,

    pm.bind('sbEditUserProfileModal', function(data) {
        console.log(data);
    });

### sbEditUserProfileModal

Note: this response has been trimmed for brevity

    {
      "user": {
        "id": 32518,
        "url": "https://www.fullscreendirect.com/user/mycoolusername",
        "created": "2009-10-27 14:29:16",
        "name": "Jake Smith",
        "username": "mycoolusername",
        "bio": "Baseball and batman",
        "color": "70,170,255",
        "email": "hi@stagebloc.com",
        "gender": "male",
        "birthday": "1923-07-05",
        "photo": {
          "id": 23170,
          "width": 500,
          "height": 410,
          "images": {
            "thumbnail_url": "",
            "small_url": "",
            "medium_url": "",
            "large_url": ""
          }
        }
      }
    }

### sbComment

    {
        "contentSlug": "blog",
        "contentId": 2516,
        "likeCount": 52,
        "commentCount": 129
    }

### sbCartAddedItem

    {
        "id": 4201,
        "name": "My Cool Item",
        "price": 14.14
    }

### sbCartBeginCheckout

    {
        "items": [
            {
                "id": 4201,
                "name": "My Cool Item",
                "price": 14.14,
                "quantity": 1
            },
            {
                "id": 1211,
                "name": "Another cool Item!!!",
                "price": 5,
                "quantity": 2
            }
        ],
        "subtotal": 24.14
    }

### sbCartSuccess

    {
        "google_analytics": {
            "items": [
                {
                    "name": "My Cool Item",
                    "sku": "Main",
                    "price": 14.14,
                    "quantity": 1,
                    "currency": "usd"
                },
                {
                    "name": "Another cool Item!!!",
                    "sku": "Main",
                    "price": 5,
                    "quantity": 2,
                    "currency": "usd"
                },
                {
                    "name": "Cat Shirt",
                    "sku": "Medium",
                    "price": 50,
                    "quantity": 1,
                    "currency": "usd"
                }
            ],
            "transaction": {
                "revenue": 74.14,
                "tax": 12.11,
                "shipping": 10
            }
        }
    }

## Search
Searching for your content within Fullscreen Direct is possible with JavaScript in your theme. You simply make a `POST` request to `/_search` with your request, and a JSON blob will be returned with the search results. Make sure that `/_search` occurs at the root of your URL, e.g. `custom_domain.com/_search` or `www.fullscreendirect.com/<account_url>/_search`

    $('.js-search').click(function() {
        $.ajax({
            type: 'post',
            dataType: 'json',
            data: {
                q: '<query string here>',
                offset: 0,
                limit: 50,
                filter: `<comma separated string of content types here>`
            },
            url: '_search',
            success: function(data) {

            }
        });
    });

The structure of the returned data will be:

    {
        "metadata": {
            "offset": 0,
            "limit": 50,
            "total": 56
        },
        "data": [{
            "content": { ... will be an array representation of an object the same way the API structures it ... },
            "score": 2.1433315
        }]
    }

## Custom Fields
Custom Fields is a feature that allows you to attach extra pieces of information to content within Fullscreen Direct that we don't natively support. For instance, if you wanted to be able to mark events as sold out, something we won't natively support, then you could create a custom field to add a boolean flag to events marking whether or not they are sold out.

To start using Custom Fields, you must first set some up in the advanced part of the backend account management section. You are currently able to setup the following types of custom fields:

    - Textarea - Use this to add blurbs of text to pieces of content
    - Select - Use this to add dropdowns with pre-defined options to pieces of content
    - Checkbox - Can be used to add a boolean flag to content
    - Input - Useful for adding a short amount of text to content
    - URL - Similar to a normal Input but more geared towards a URL
    - Photo - Can be used to add photos to content (i.e. a header image for blog posts, etc)
    - HTML - Used to add chunks of HTML code to content
    
Once you setup a custom field on a piece of content, you'll begin to see them in the areas you normally edit that content. For instance, on photo modals you'll see a "Custom Fields" collapsable toggle that allows you to edit any custom fields that were setup for photos.

Getting the data from custom fields into your theme is pretty straightforward. Below are some of the options you have available to you in the theming engine for them:

**if:CustomFieldIsSet**  
Use this to check if the custom field for the content in question has been explicitly set when editing the content or not.

`slug` (*required*)

    the slug of the custom field you are checking, this can be seen in the backend once they are created
    
    accepted values are any slug that you have setup for custom fields
    
    defaults to none
    
**CustomField**  
This variable can be used to access the data of custom field's (i.e. the text, URL, etc that was entered for it)

`slug` (*required*)

    the slug of the custom field you are checking, this can be seen in the backend once they are created
    
    accepted values are any slug that you have setup for custom fields
    
    defaults to none
    
For example, here's how you could accomplish the sold out functionality that was mentioned earlier:
    
    {if:CustomFieldIsSet slug="is-sold-out"}
        <div class="sold-out">
            <img class="sold-out-img" src="http://stgb.lc/ta/20130721_054345_2632_516.png" />
        </div>
    {/if:CustomFieldIsSet}

## User Profile Editing
This allows the user's profile to be edited.

**Step One**

First, you'll need some sort way of having users add information. For instance, you could do:

    {module:UserView loggedIn="true"}
        {block:UserView}
        <form id="userEditForm">
            <input type="hidden" name="email" value="{UserEmail}" />
            <input type="hidden" name="username" value="{UserUsername}" />
            <textarea name="bio">{UserBio}</textarea>
            <input type="submit" value="Save Profile" />
        </form>
        {/block:UserView}
    {/module:UserView}

The available keys for passing are `name`, `username`, `email`, `bio`, and `gender`. You can also pass a `birthday` parameter as an array with the keys `day`, `month`, and `year`.

Note that if you are changing the email of the user, you must also ask the user for their current password and pass `currentPassword` with your request.

You are also able to pass a user photo with the `photoFile` key, but it requires that the browser support `FileReader`. When a `file` input is changed, you can show a preview of the image on the page using code similar to the following:

    if (typeof FileReader === 'undefined') {
      alert('Sorry, but your browser is too old to upload photos. Please upgrade!');
      return;
    }

    var reader = new FileReader();
    reader.onload = function(event) {
      _userPhotoData = event.target.result; // A global variable for holding this image
      $('#userPhotoPreview').css('background-image', 'url(' + newPhotoData + ')');
    };
    reader.readAsDataURL(this.files[0]);

**Step Two**

Next, when the user submits the form, you'll want to capture it with JavaScript similar to the following:

    $('#userEditForm').submit(function() {
        var formData = $(this).serialize() + '&photoFile=' + _userPhotoData;
        pm({ target: window.frames.sbnav, type: 'sbInlineUserProfileEdit', data: formData });
        return false;
    });

**Step Three**

Finally, you should add a binding to your theme's JavaScript. You can use this callback to handle the data returned from SBNav.

    pm.bind('sbInlineUserProfileEdit', function(data) {
        // data will be a JavaScript object representing the user (or an error if an error occurred)
    });

## User Password Resetting
This allows a user to initiate a password reset if they forgot their password. The system will then send them an email with a reset link. You can optionally choose to handle the reset page otherwise they will be taken through the normal Fullscreen Direct password resetting flow.

**Step One**

First, you'll need some sort way of having users give you their information to initiate a reset with. For instance, you could do:

    <form id="forgotPasswordForm">
        <input type="text" name="email" />
        <input type="submit" value="Send Password Reset Email" />
    </form>

The available keys for passing are simply `email`.

**Step Two**

Next, when the user submits the form, you'll want to capture it with JavaScript similar to the following:

    $('form#forgotPasswordForm').on('submit', function (e) {
        e.preventDefault();
        pm({target: window.frames.sbnav, type: 'sbInlineUserForgotPassword', data: {
                email: $(this).find('[name="email"]').val()
            }
        });
    });

**Step Three**

Finally, you should add a binding to your theme's JavaScript. You can use this callback to handle the data returned from SBNav.

    pm.bind('sbInlineUserForgotPassword', function(data) {
        // data will be a JavaScript object representing success or failure
    });

**Step Four** _(optional beyond here)_

This page in the theming engine (`{page:PasswordReset}`) will be linked to in the user's password reset email if your theme has it defined. The `{GET}` variable can be used to automatically get the hash from the URL.

    {page:PasswordReset}
        <form class="js-reset-password">
            <input type="hidden" name="hash" value="{GET param="hash"}" />
            <input type="text" name="email" placeholder="Email Address" />
            <input type="password" name="password" placeholder="Password" />
            <input type="password" name="confirmPassword" placeholder="Confirm Password" />

            <input type="submit" />
        </form>
    {/page:PasswordReset}

**Step Five**

Once the user enter their information (and the hash from the URL is inserted into the form), you can capture it's submission to do the actual password reset. The keys for the passed data should match the names of the form elements in step four.

    $passwordResetForm.submit(function() {
        pm({target: window.frames.sbnav, type: 'sbInlineUserPasswordReset', data: $(this).serialize() });
        return false;
    });

**Step Six**

Once the system determines if this password reset is valid, you'll receive an error or success message back to your listener like below.

    pm.bind('sbInlineUserPasswordReset', function(data) {
        if (data.error) {
            alert(data.error);
        } else {
            alert(data.success);
        }
    });

## Email List Subscription
This allows accounts to capture emails for a generic email list signup. This is currently only available for paid accounts.

**Step One**

First, you'll need some sort way of having users enter their information. For instance, you could do:

    <form id="emailForm">
        <input type="text" name="name" />
        <input type="text" name="email" />
        <input type="submit" value="Subscribe to email list" />
    </form>

The available keys for passing are `name`, `email`, `emailListId`, and `country` (two digit code). As such, you'll also need to have made a generic email list in the Fullscreen Direct backend so that you can use the list's ID here (required). A birthday can also be passed through with the input `name` being `birthday[month]`, `birthday[day]`, and `birthday[year]`

**Step Two**

Next, when the user submits the form, you'll want to capture it with JavaScript similar to the following:

    $('form#emailForm').on('submit', function (e) {
        e.preventDefault();
        pm({target: window.frames.sbnav, type: 'sbInlineEmailListSignup', data: {
                name: $(this).find('[name="name"]').val(),
                email: $(this).find('[name="email"]').val(),
                emailListId: 1
            }
        });
    });

**Step Three**

Finally, you should add a binding to your theme's JavaScript. You can use this callback to handle the data returned from SBNav.

    pm.bind('sbInlineEmailListSignup', function(data) {
        // data will be a JavaScript object representing success or failure
    });

## Event RSVP
This allows users to change their RSVP status for an event

**Step One**

First, you'll need some sort way of having users change their status. For instance, you could do:

    <button type="button" data-rsvp="yes" class="js-rsvp-button {if:UserEventAttendingStatus status="yes"}btn-s-active{/if:UserEventAttendingStatus}">Attending</button>

    <button type="button" data-rsvp="maybe" class="js-rsvp-button {if:UserEventAttendingStatus status="maybe"}btn-s-active{/if:UserEventAttendingStatus}">Maybe attending</button>

    <button type="button" data-rsvp="no" class="js-rsvp-button {if:UserEventAttendingStatus status="no"}btn-s-active{/if:UserEventAttendingStatus}">Not attending</button>

The available options to pass are "yes", "no", and "maybe".

**Step Two**

Next, when the user clicks the button, you'll want to pass along that information with JavaScript similar to the following:

    $('.js-rsvp-button').on('click', function (e) {
        e.preventDefault();
        pm({target: window.frames.sbnav, type: 'sbInlineEventRSVP', data: {
                eventId: 1,
                status: $(this).attr('data-rsvp')
            }
        });
    });

**Step Three**

Finally, you should add a binding to your theme's JavaScript. You can use this callback to handle the data returned from SBNav.

    pm.bind('sbInlineEventRSVP', function(data) {
        // data will be a JavaScript object representing success or failure
    });

## Contest Entry
This allows users to enter in to contests you are running through your account.  

**Step One**

First, you'll need some sort way of having users enter their information. For instance, you could do:

    <form id="contestEntryForm">
        <input type="text" name="response" id="response" />
        <input type="submit" value="Enter Contest" />
    </form>

The available keys for passing are `response`, `contestId`, and `participate`. Participate is simply a boolean flag for whether the user is entering or removing themselves from the contest. The response field is optional and only applies if the contest itself has a prompt set up.

**Step Two**

Next, when the user submits the form, you'll want to capture it with JavaScript similar to the following:

    $('form#contestEntryForm').on('submit', function (e) {
        e.preventDefault();
        pm({target: window.frames.sbnav, type: 'sbInlineEditContestParticipation', data: {
                participate: true,
                response: $(this).find('#response').val(),
                contestId: 1
            }
        });
    });

**Step Three**

Finally, you should add a binding to your theme's JavaScript. You can use this callback to handle the data returned from SBNav.

    pm.bind('sbInlineEditContestParticipation', function(data) {
        // data will be a JavaScript object representing success or failure
    });

## Commenting
Normally, adding comments can be done very easily with the use of the `{CommentLink}` variable that opens an SBNav modal. However, sometimes you'll want to have commenting inline within your page to give the user a different experience.

**Step One**

First, need some sort of `HTML` to get the necessary data. Here's an example:

    <form id="commentForm">
        <input type="hidden" name="content_slug" value="{ContentType-Photos}" />
        <input type="hidden" name="content_id" value="{PhotoId}" />
        <textarea name="commentText"></textarea>
        <input type="submit" value="Post Comment" />
    </form>

Note that `content_id` and `content_slug` will vary depending on the content you're trying to comment on and will most likely be received from some sort of `{block}` data within your theme.

There is also an optional `replyToId` parameter that can be passed to serve as the `id` of the original comment if this new comment is meant to be a reply to it.

**Step Two**

Next, when the user submits the form, you'll want to capture it with JavaScript similar to the following:

    $('#commentForm').submit(function() {
        pm({ target: window.frames.sbnav, type: 'sbInlineComment', data: $(this).serialize() });
        return false;
    });
    
The passed data must contain the keys `content_slug`, `content_id`, and `commentText` with their respective values for it to be valid.

**Step Three**

Finally, you should add a binding to your theme's JavaScript. You can use this callback to handle the data returned from SBNav.

    pm.bind('sbInlineComment', function(data) {
        // data will be a JavaScript representing the comment that was posted (or an error if an error occurred)
    });

## Comment Loading
Normally, viewing comments can be done very easily with the use of the `{CommentLink}` variable that opens an SBNav modal. However, sometimes you'll want to have comments inline within your page to give the user a different experience. Initial comments can be loaded with `{module:CommentList}`, but you'll want to be careful not to load every single comment on the initial page load. This advanced functionality can be used to allow for loading more comments after the initial page load.

**Step One**

You'll want to make a call to the `sbInlineLoadComments` functionality of SBNav:

    pm({ target: window.frames.sbnav,
         type: 'sbInlineLoadComments',
         data: { contentType: $(this).attr('data-content-type'), // i.e. {ContentType-Photos}
                 contentId: $(this).attr('data-content-id'),
                 limit: 20, // Any number
                 offset: 10 // Any number, probably based on the amount of comments already loaded
         }
    });

The passed data must contain the keys `contentType` and `contentId` to be valid.

**Step Two**

Finally, you should add a binding to your theme's JavaScript. You can use this callback to handle the data returned from SBNav.

    pm.bind('sbInlineLoadComments', function(data) {
        // data will be a JavaScript array representing the additional comments (or an error if an error occurred)
    });
    
## Status Submission
Normally, adding statuses can be done very easily with the use of `{SubmitFanContentLink}` variable that opens an SBNav modal. However, sometimes you'll want to allow fans to submit statuses for your fansite from within the theme itself.

**Step One**

First, you'll need some sort of `HTML` form. Here's an example:

    <form id="statusSubmit">
        <input type="text" name="statusText" />
        <input type="submit" />
    </form>

**Step Two**

Next, when the user submits the form, you'll want to capture it with JavaScript similar to the following:

    $('#statusSubmit').submit(function() {
        pm({ target: window.frames.sbnav, type: 'sbInlineSubmitStatus', data: $(this).serialize() });
        return false;
    });
    
The passed data must contain the key `statusText` for it to be valid.

**Step Three**

Finally, you should add a binding to your theme's JavaScript. You can use this callback to handle the data returned from SBNav.

    pm.bind('sbInlineSubmitStatus', function(data) {
        // data will be a JavaScript object representing the status that was posted (or an error if an error occurred)
    });

## Photo Submission
Normally, adding photo can be done very easily with the use of `{SubmitFanContentLink}` variable that opens an SBNav modal. However, sometimes you'll want to allow fans to submit photos for your fansite from within the theme itself. Note that this method does require that the user's browser supports `FileReader`.

**Step One**

First, you'll need some sort of `HTML` form. Here's an example:

    <form id="photoSubmit">
        <input type="file" name="photoFile" id="photoFile" />
        <input type="submit" />
    </form>
    
When this `file` input changes, you'll want to put the data for that image somewhere on the page where you can get it later or store it in a global variable. Below is an example of how to do that as well as how to show the user a preview of the image:

    $('body').on('change', '#photoFile', function() {
        if (typeof FileReader === 'undefined') {
          alert('Sorry, but your browser is too old to upload photos. Please upgrade!');
          return;
        }

        var reader = new FileReader();
        reader.onload = function(event) {
            var img = new Image();
            img.src = event.target.result;
            $('#photoPreview').html(img);
            $('#userPhotoPreview').css('background-image', 'url(' + newPhotoData + ')');
        };
        reader.readAsDataURL(this.files[0]);
    }

**Step Two**

Next, when the user submits the form, you'll want to capture it with JavaScript similar to the following:

    $('#photoSubmit').submit(function() {
        var formData = $(this).serialize() + '&photoFile=' + $('#photoPreview img').attr('src');
        pm({ target: window.frames.sbnav, type: 'sbInlineSubmitPhoto', data: formData });
        return false;
    });

The passed data must contain the key `statusText` for it to be valid.

**Step Three**

Finally, you should add a binding to your theme's JavaScript. You can use this callback to handle the data returned from SBNav.

    pm.bind('sbInlineSubmitPhoto', function(data) {
        // data will be a JavaScript object representing the photo that was posted (or an error if an error occurred)
    });

## Blog Submission
Normally, adding blogs can be done very easily with the use of `{SubmitFanContentLink}` variable that opens an SBNav modal. However, sometimes you'll want to allow fans to submit blogs for your fansite from within the theme itself.

**Step One**

First, you'll need some sort of `HTML` form. Here's an example:

    <form id="blogSubmit">
        <input type="text" name="blogTitle" />
        <textarea name="blogBody"></textarea>
        <input type="submit" />
    </form>

**Step Two**

Next, when the user submits the form, you'll want to capture it with JavaScript similar to the following:

    $('#blogSubmit').submit(function() {
        pm({ target: window.frames.sbnav, type: 'sbInlineSubmitBlog', data: $(this).serialize() });
        return false;
    });
    
The passed data must contain the keys `blogTitle` and `blogBody` for it to be valid. You can also pass a comma separated list of words to use as tags with the key `blogTags`.

**Step Three**

Finally, you should add a binding to your theme's JavaScript. You can use this callback to handle the data returned from SBNav.

    pm.bind('sbInlineSubmitBlog', function(data) {
        // data will be a JavaScript object representing the blog that was posted (or an error if an error occurred)
    });

## Video Submission
Normally, adding videos can be done very easily with the use of `{SubmitFanContentLink}` variable that opens an SBNav modal. However, sometimes you'll want to allow fans to submit videos for your fansite from within the theme itself.

**Step One**

First, you'll need some sort of `HTML` form. Here's an example:

    <form id="videoSubmit">
        <input type="text" name="videoRawUrl" />
        <input type="submit" />
    </form>

**Step Two**

Next, when the user submits the form, you'll want to capture it with JavaScript similar to the following:

    $('#videoSubmit').submit(function() {
        pm({ target: window.frames.sbnav, type: 'sbInlineSubmitVideo', data: $(this).serialize() });
        return false;
    });
    
The passed data must contain the key `videoRawUrl` for it to be valid (we'll then use oEmbed to grab the data for the video). You can explicitly pass `videoTitle` if you'd like. You can also pass a comma separated list of words to use as tags with the key `videoTags`.

**Step Three**

Finally, you should add a binding to your theme's JavaScript. You can use this callback to handle the data returned from SBNav.

    pm.bind('sbInlineSubmitVideo', function(data) {
        // data will be a JavaScript object representing the video that was posted (or an error if an error occurred)
    });
