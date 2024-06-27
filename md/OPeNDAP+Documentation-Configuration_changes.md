### LocalSettings.php hacks

    # This snippet prevents new registrations from anonymous users
    # (Sysops can still create user accounts)
    $wgGroupPermissions['*']['createaccount'] = false;

    # This prevents anonymous users from editing.
    $wgGroupPermissions['*']['edit'] = false;
    require_once( "includes/DefaultSettings.php" );

    # Types of files which may be uploaded.
    $wgFileExtensions = array( 'png', 'gif', 'jpg', 'jpeg', 'pdf', 'txt', 'ppt');

    # OPeNDAP Logo
    $wgLogo = "http://www.opendap.org/images/OPeNDAP-meatball.png";
    $wgFavicon = "http://www.opendap.org/favicon.ico";

    # Allow raw HTML. Normally 'dangerous' but we limit edits so it should be OK
    $wgRawHtml = true;

    # disable anonomous talk
    $wgDisableAnonTalk = true;

    # Set the article path
    #$wgArticlePath = "/$1";

### 3/20/2007

Trying to increase the limit on file uploads. This hasn't worked so far.

`Changed MAX_FILE_SIZE in SpecialImport.php to 10485760`

I also tried various things with `/usr/local/lib/php.ini` without any
luck.

See <http://meta.wikimedia.org/wiki/Uploading_files> and
<http://us3.php.net/manual/en/ini.core.php#ini.upload-max-filesize>.

### 4/18/2007

I changed the message "Log in / create account" on th log in page to
"Log in" since that seemed more appropriate by editing the message on
[*System messages*](Special:Allmessages "wikilink") as described in the
file *./languages/messages/MessagesEn.php*.

I also added some text to the *includes/templates/Userlogin.php* page so
that people that try to login without an account are asked to request an
account.