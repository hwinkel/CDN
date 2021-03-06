---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-07"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:download: .download}

# Input Container
The Input Container is a collection utilized by both the Mapping and (Origin) Path classes.  It provide a consistent set of input attributes for both classes.

* `vendorName`: The name of a valid IBM Cloud CDN provider.
* `oldPath`: Used by updateOriginPath(). This property stores the name of the current, or 'old', path.

The following attributes are common to the Mapping and (Origin) Path classes:
* `originType`: Type of the Origin host, currently 'HOST_SERVER' or 'OBJECT_STORAGE'.
* `origin`: Origin server address (either the hostname or the IPv4 address of the Origin Server), the endpoint from which to fetch content, or the name of the bucket where content is stored. Must be less than 511 characters.
* `httpPort`: Number of the port used for HTTP protocol. Akamai has certain limitations on port numbers for HTTP and HTTPS ports. Please see the [FAQ](faq.html#are-there-any-restrictions-on-what-http-and-https-port-numbers-are-allowed-for-akamai-) for a list of allowed port numbers.
* `httpsPort`: Number of the port used for HTTPS protocol. Akamai has certain limitations on port numbers for HTTP and HTTPS ports. Please see the [FAQ](faq.html#are-there-any-restrictions-on-what-http-and-https-port-numbers-are-allowed-for-akamai-) for a list of allowed port numbers.
* `status`:  The status of the mapping or path. Status can be CNAME_CONFIGURATION, SSL_CONFIGURATION, RUNNING, STOPPED, DELETED, or ERROR.
* `path`: Path from which the cached content will be served. The default path is /\* When used by the `updateOriginPath` API, this attribute refers to the new path to be added.
* `performanceConfiguration`: Specifications for the performance configuration of the mapping.
* `cacheKeyQueryRule`: The following options are available to configure Cache Key behavior:
  * `include-all`: Include all query arguments
  * `ignore-all`: Ignore all query arguments
  * `ignore: space separated query-args`: Ignores those specific query arguments. For example, `ignore: query1 query2`
  * `include: space separated query-args`: Includes those specific query arguments. For example, `include: query1 query2`

The following attributes are specific to the Mapping class:

* `uniqueId`: A 10-digit, system-generated, identifier that is unique to each mapping. Generated when a mapping is created.
* `domain`: Primary CDN name. Also referred to as host name.
* `protocol`: Protocol used to set up services. Can be HTTP, HTTPS or a combination of the two, HTTP_AND_HTTPS.
* `cname`: Canonical name record aliases the hostname. May be provided by the user, or system-generated. If user-provided, it should be less than 64 alphanumeric characters and must be unique. If not provided, one will be generated when the mapping is created.
* `certificateType`: Type of certificate being used by a mapping. May be `WILDCARD_CERT` or `SHARED_SAN_CERT`. Value will be 0 for HTTP mappings.
* `respectHeaders`: A Boolean value that, if set to 'true', causes TTL settings in the Origin to override CDN TTL settings.
* `header`: Specifies host header information used by the Origin.

The following attributes are related to Cloud Object Storage (COS):  
* `bucketName`: Unique name of your bucket for S3 object storage.  
* `fileExtension`: File extensions that are allowed.
