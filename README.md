WordPress .htaccess
==================

The ultimate WordPress .htaccess file

```

# BEGIN WordPress

RewriteEngine On
RewriteBase /
RewriteRule ^index\.php$ - [L]

# add a trailing slash to /wp-admin
RewriteRule ^wp-admin$ wp-admin/ [R=301,L]

RewriteCond %{REQUEST_FILENAME} -f [OR]
RewriteCond %{REQUEST_FILENAME} -d
RewriteRule ^ - [L]
RewriteRule ^(wp-(content|admin|includes).*) $1 [L]
RewriteRule ^(.*\.php)$ $1 [L]
RewriteRule . index.php [L]

# END WordPress

# ##############################################################################
# # WEB PERFORMANCE                                                            #
# ##############################################################################

# ------------------------------------------------------------------------------
# | Compression                                                                |
# ------------------------------------------------------------------------------

<IfModule mod_deflate.c>

    # Force compression for mangled headers.
    # https://developer.yahoo.com/blogs/ydn/pushing-beyond-gzipping-25601.html
    <IfModule mod_setenvif.c>
        <IfModule mod_headers.c>
            SetEnvIfNoCase ^(Accept-EncodXng|X-cept-Encoding|X{15}|~{15}|-{15})$ ^((gzip|deflate)\s*,?\s*)+|[X~-]{4,13}$ HAVE_Accept-Encoding
            RequestHeader append Accept-Encoding "gzip,deflate" env=HAVE_Accept-Encoding
        </IfModule>
    </IfModule>

    # Compress all output labeled with one of the following media types
    # (for Apache versions below 2.3.7, you don't need to enable `mod_filter`
    #  and can remove the `<IfModule mod_filter.c>` and `</IfModule>` lines
    #  as `AddOutputFilterByType` is still in the core directives).
    <IfModule mod_filter.c>
        AddOutputFilterByType DEFLATE application/atom+xml \
                                      application/javascript \
                                      application/json \
                                      application/ld+json \
                                      application/manifest+json \
                                      application/rss+xml \
                                      application/vnd.ms-fontobject \
                                      application/x-font-ttf \
                                      application/x-font-opentype \
                                      application/x-web-app-manifest+json \
                                      application/xhtml+xml \
                                      application/xml \
                                      font/opentype \
                                      image/svg+xml \
                                      image/x-icon \
                                      text/css \
                                      text/html \
                                      text/plain \
                                      text/javascript \
                                      text/vtt \
                                      text/x-component \
                                      text/xml
    </IfModule>

</IfModule>


# ------------------------------------------------------------------------------
# | Vary Accept-Encoding                                                       |
# ------------------------------------------------------------------------------
<IfModule mod_headers.c>
  <FilesMatch "\.(js|css|xml|gz)$">
    Header append Vary: Accept-Encoding
  </FilesMatch>
</IfModule>

# ------------------------------------------------------------------------------
# | ETags                                                                      |
# ------------------------------------------------------------------------------

# Remove `ETags` as resources are sent with far-future expires headers.
# https://developer.yahoo.com/performance/rules.html#etags

# `FileETag None` doesn't work in all cases.
<IfModule mod_headers.c>
    Header unset ETag
</IfModule>

FileETag None

# ------------------------------------------------------------------------------
# | Expires headers                                                            |
# ------------------------------------------------------------------------------

# Serve resources with far-future expires headers.

# IMPORTANT: If you don't control versioning with filename-based cache
# busting, consider lowering the cache times to something like one week.

<IfModule mod_expires.c>

    ExpiresActive on
    ExpiresDefault                                      "access plus 1 month"

  # CSS
    ExpiresByType text/css                              "access plus 1 year"

  # Data interchange
    ExpiresByType application/json                      "access plus 0 seconds"
    ExpiresByType application/ld+json                   "access plus 0 seconds"
    ExpiresByType application/vnd.geo+json              "access plus 0 seconds"
    ExpiresByType application/xml                       "access plus 0 seconds"
    ExpiresByType text/xml                              "access plus 0 seconds"

  # Favicon (cannot be renamed!) and cursor images
    ExpiresByType image/x-icon                          "access plus 1 week"

  # HTML components (HTCs)
    ExpiresByType text/x-component                      "access plus 1 month"

  # HTML
    ExpiresByType text/html                             "access plus 0 seconds"

  # JavaScript
    ExpiresByType application/javascript                "access plus 0 seconds"
    ExpiresByType text/javascript                		"access plus 0 seconds"
    

  # Manifest files
    ExpiresByType application/manifest+json             "access plus 1 year"
    ExpiresByType application/x-web-app-manifest+json   "access plus 0 seconds"
    ExpiresByType text/cache-manifest                   "access plus 0 seconds"

  # Media
    ExpiresByType audio/ogg                             "access plus 1 month"
    ExpiresByType image/gif                             "access plus 1 month"
    ExpiresByType image/jpeg                            "access plus 1 month"
    ExpiresByType image/png                             "access plus 1 month"
    ExpiresByType video/mp4                             "access plus 1 month"
    ExpiresByType video/ogg                             "access plus 1 month"
    ExpiresByType video/webm                            "access plus 1 month"

  # Web feeds
    ExpiresByType application/atom+xml                  "access plus 1 hour"
    ExpiresByType application/rss+xml                   "access plus 1 hour"

  # Web fonts
    ExpiresByType application/font-woff                 "access plus 1 month"
    ExpiresByType application/font-woff2                "access plus 1 month"
    ExpiresByType application/vnd.ms-fontobject         "access plus 1 month"
    ExpiresByType application/x-font-ttf                "access plus 1 month"
    ExpiresByType font/opentype                         "access plus 1 month"
    ExpiresByType image/svg+xml                         "access plus 1 month"

</IfModule>

# ##############################################################################
# # MEDIA TYPES AND CHARACTER ENCODINGS                                        #
# ##############################################################################

# ------------------------------------------------------------------------------
# | Media types                                                                |
# ------------------------------------------------------------------------------

# Serve resources with the proper media types (formerly known as MIME types).
# http://www.iana.org/assignments/media-types/media-types.xhtml

<IfModule mod_mime.c>

  # Audio
    AddType audio/mp4                                   m4a f4a f4b
    AddType audio/ogg                                   oga ogg opus

  # Data interchange
    AddType application/json                            json map
    AddType application/ld+json                         jsonld

  # JavaScript
    # Normalize to standard type.
    # http://tools.ietf.org/html/rfc4329#section-7.2
    AddType application/javascript                      js
    AddType text/javascript                      		js

  # Manifest files

    # If you are providing a web application manifest file (see the
    # specification: http://w3c.github.io/manifest/), it is recommended
    # that you serve it with the `application/manifest+json` media type.
    #
    # Because the web application manifest file doesn't have its own
    # unique file extension, you can set its media type either by matching:
    #
    # 1) the exact location of the file (this can be done using a directive
    #    such as `<Location>`, but it will NOT work in the `.htaccess` file,
    #    so you will have to do it in the main server configuration file or
    #    inside of a `<VirtualHost>` container)
    #
    #    e.g.:
    #
    #       <Location "/.well-known/manifest.json">
    #           AddType application/manifest+json               json
    #       </Location>
    #
    # 2) the filename (this can be problematic as you will need to ensure
    #    that you don't have any other file with the same name as the one
    #    you gave to your web application manifest file)
    #
    #    e.g.:
    #
    #       <Files "manifest.json">
    #           AddType application/manifest+json               json
    #       </Files>

    AddType application/x-web-app-manifest+json         webapp
    AddType text/cache-manifest                         appcache manifest

  # Video
    AddType video/mp4                                   f4v f4p m4v mp4
    AddType video/ogg                                   ogv
    AddType video/webm                                  webm
    AddType video/x-flv                                 flv

  # Web fonts
    AddType application/vnd.ms-fontobject 				.eot
	AddType application/x-font-ttf 						.ttf
	AddType application/x-font-opentype 				.otf
	AddType application/x-font-woff 					.woff
	AddType image/svg+xml 								.svg .svgz

    # Browsers usually ignore the font media types and simply sniff
    # the bytes to figure out the font type.
    # http://mimesniff.spec.whatwg.org/#matching-a-font-type-pattern

    # Chrome however, shows a warning if any other media types are used
    # for the following fonts.

    AddType application/x-font-ttf                      ttc ttf
    AddType font/opentype                               otf

    # Make SVGZ fonts work on the iPad.
    # https://twitter.com/FontSquirrel/status/14855840545
    AddEncoding gzip                                    svgz

  # Other
    AddType application/octet-stream                    safariextz
    AddType application/x-chrome-extension              crx
    AddType application/x-opera-extension               oex
    AddType application/x-xpinstall                     xpi
    AddType application/xml                             atom rdf rss xml
    AddType image/webp                                  webp
    AddType image/x-icon                                cur ico
    AddType text/vtt                                    vtt
    AddType text/x-component                            htc
    AddType text/x-vcard                                vcf

</IfModule>

# ------------------------------------------------------------------------------
# | Character encodings                                                        |
# ------------------------------------------------------------------------------

# Set `UTF-8` as the character encoding for all resources served with
# the media type of `text/html` or `text/plain`.
AddDefaultCharset utf-8

# Set `UTF-8` as the character encoding for other certain resources.
<IfModule mod_mime.c>
    AddCharset utf-8 .atom .css .js .json .jsonld .rss .vtt .webapp .xml
</IfModule>

# ##############################################################################
# # MANUAL 301 REDIRECTS                                                       #
# ##############################################################################

#redirect 301 /old-page-url.html https://www.domain.co.uk/new-page-url

# ##############################################################################
# # INTERNET EXPLORER                                                          #
# ##############################################################################

# ------------------------------------------------------------------------------
# | Better website experience                                                  |
# ------------------------------------------------------------------------------

# Force Internet Explorer to render pages in the highest available mode
# in the various cases when it may not.
# https://hsivonen.fi/doctype/ie-mode.pdf
# https://hsivonen.fi/doctype/#ie8

<IfModule mod_headers.c>
    Header set X-UA-Compatible "IE=edge"
    # `mod_headers` cannot match based on the content-type, however, this header
    # should be send only for HTML documents and not for the other resources
    <FilesMatch "\.(appcache|atom|crx|css|cur|eot|f4[abpv]|flv|gif|htc|ico|jpe?g|js|json(ld)?|m4[av]|manifest|map|mp4|oex|og[agv]|opus|otf|pdf|png|rdf|rss|safariextz|svgz?|swf|tt[cf]|txt|vcf|vtt|webapp|web[mp]|woff|xml|xpi)$">
        Header unset X-UA-Compatible
    </FilesMatch>
</IfModule>

# ##############################################################################
# # SECURITY                                                                   #
# ##############################################################################

# ------------------------------------------------------------------------------
# | Prevent directory browsing                                                 |
# ------------------------------------------------------------------------------

<IfModule mod_autoindex.c>
  Options -Indexes
</IfModule>

# ------------------------------------------------------------------------------
# | Prevent .htaccess access                                                   |
# ------------------------------------------------------------------------------

<files .htaccess>
    order allow,deny
    deny from all
</files>

# ------------------------------------------------------------------------------
# | Disable HTTP Trace                                                         |
# ------------------------------------------------------------------------------

RewriteEngine On
RewriteCond %{REQUEST_METHOD} ^TRACE
RewriteRule .* - [F]

# ------------------------------------------------------------------------------
# | Block access to hidden files & directories                                 |
# ------------------------------------------------------------------------------

<IfModule mod_rewrite.c>
    RewriteCond %{SCRIPT_FILENAME} -d [OR]
    RewriteCond %{SCRIPT_FILENAME} -f
    RewriteRule "(^|/)\." - [F]
</IfModule>

# ------------------------------------------------------------------------------
# | Block access to source files                                               |
# ------------------------------------------------------------------------------

<FilesMatch "(^#.*#|\.(bak|conf|dist|fla|in[ci]|log|psd|sh|sql|sw[op])|~)$">

    # Apache < 2.3
    <IfModule !mod_authz_core.c>
        Order allow,deny
        Deny from all
        Satisfy All
    </IfModule>

    # Apache ≥ 2.3
    <IfModule mod_authz_core.c>
        Require all denied
    </IfModule>

</FilesMatch>

# Block access to WordPress files that reveal version information.

<FilesMatch "^(wp-config\.php|readme\.html|license\.txt)">

    # Apache < 2.3
    <IfModule !mod_authz_core.c>
        Order allow,deny
        Deny from all
        Satisfy All
    </IfModule>

    # Apache ≥ 2.3
    <IfModule mod_authz_core.c>
        Require all denied
    </IfModule>
</FilesMatch>
```
