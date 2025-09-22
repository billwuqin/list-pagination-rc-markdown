---
title: "RESTCONF Extensions to Support List Pagination"
abbrev: "RESTCONF Pagination Support"
category: std

docname: draft-ietf-netconf-list-pagination-rc-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Network Configuration"
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger

author:
 -
    fullname:  Kent Watsen
    organization: Watsen Networks
    email: kent+ietf@watsen.net
 -
    fullname:  Qin Wu
    organization: Huawei
    email: bill.wu@huawei.com
 -
    fullname:  Per Andersson
    organization: Cisco
    email: per.ietf@ionio.se
 -
    fullname:  Olof Hagsand
    organization: SUNET
    email: olof@hagsand.se
 -
    fullname:  Hongwei Li
    organization: HP
    email: flycoolman@gmail.com

normative:

informative:

...

--- abstract

   This document defines a mapping of the list pagination mechanism
   defined in {{?I-D.ietf-netconf-list-pagination}} to RESTCONF {{!RFC8040}}.

   This document updates RFC 8040, to declare "list" and "leaf-list" as
   valid resource targets for the RESTCONF GET operation, to define GET
   query parameters necessary for list pagination, and to define a
   media-type for XML-based lists.


--- middle

# Introduction

   This document defines a mapping of the list pagination mechanism
   defined in {{?I-D.ietf-netconf-list-pagination}} to RESTCONF {{!RFC8040}}.

   This document updates RFC 8040, as described in Section 2.
   Declaring "list" and "leaf-list" as valid resource targets for the
   GET operation is necessary for list pagination.

## Terminology

   The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
   "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
   "OPTIONAL" in this document are to be interpreted as described in BCP
   14 {{!RFC2119}} {{!RFC8174}} when, and only when, they appear in all
   capitals, as shown here.


## Conventions
{::boilerplate bcp14-tagged}

   Various examples in this document use "BASE64VALUE=" as a placeholder
   value for binary data that has been base64 encoded (per Section 9.8
   of {{?RFC7950}}).  This placeholder value is used because real base64
   encoded structures are often many lines long and hence distracting to
   the example being presented.

# Update to RFC 8040

##  Resource Targets

   This document extends Section 3.5 of {{!RFC8040}} to add "list" and
   "leaf-list" nodes (not just their entries) as valid data resources
   for the "GET" operation.

##  Media Type

   This document extends Section 3.2 of {{!RFC8040}} to add a new media
   type, "application/yang-data+xml-list", to encode "list" and "leaf-
   list" nodes in XML.

   The "application/yang-data+xml-list" media-type defines a pseudo top-
   level element called "xml-list" that is used to wrap the response
   set, thus ensuring that a single top-level element is returned for
   the XML encoding, as required by Section 4.3 of {{!RFC8040}}.

   For JSON, the existing "application/yang-data+json" media type is
   sufficient, as the JSON format has built-in support for encoding
   arrays.

   The "application/yang-data+xml-list" media type is registered in
   Section 3.2.1.

##  Query Parameters

   This document extends Section 4.8 of {{!RFC8040}} to add new query
   parameters "limit", "offset", "cursor", "direction", "sort-by",
   "locale", "where", and "sublist-limit".

   These eight query parameters correspond to those defined in Sections
   3.1 and 3.2 in {{!I-D.ietf-netconf-list-pagination}}.

~~~~
   +---------------+---------+-----------------------------------------+
   | Name          | Methods | Description                             |
   +---------------+---------+-----------------------------------------+
   | limit         | GET,    | Limits the number of entries returned.  |
   |               | HEAD    | If not specified, the number of entries |
   |               |         | that may be returned is unbounded.      |
   |               |         |                                         |
   | offset        | GET,    | Indicates the number of entries in the  |
   |               | HEAD    | result set that should be skipped over  |
   |               |         | when preparing the response.  If not    |
   |               |         | specified, then no entries in the       |
   |               |         | result set are skipped.                 |
   |               |         |                                         |
   | cursor        | GET,    | Indicates where to start the working    |
   |               | HEAD    | result set, the previous entries are    |
   |               |         | skipped over.  If not specified, then   |
   |               |         | no entries in the result set are        |
   |               |         | skipped over.                           |
   |               |         |                                         |
   | direction     | GET,    | Indicates the direction that the result |
   |               | HEAD    | set is to be traversed.  If not         |
   |               |         | specified, then the result set is       |
   |               |         | traversed in the "forwards" direction.  |
   |               |         |                                         |
   | sort-by       | GET,    | Indicates the node name that the result |
   |               | HEAD    | set should be sorted by.  If not        |
   |               |         | specified, then the result set's        |
   |               |         | default order is used, per YANG's       |
   |               |         | "ordered-by" statement.                 |
   |               |         |                                         |
   | locale        | GET,    | Specifies the locale the server should  |
   |               | HEAD    | use when collating the result set. If   |
   |               |         | not specified, the server chooses what  |
   |               |         | locale is used for collation.           |
   |               |         |                                         |
   | where         | GET,    | Specifies a filter expression that      |
   |               | HEAD    | result set entries must match.  If      |
   |               |         | not specified, then no entries are      |
   |               |         | filtered from the result set.           |
   |               |         |                                         |
   | sublist-limit | GET,    | Limits the number of entries returned   |
   |               | HEAD    | returned for descendent lists and       |
   |               |         | leaf-lists. If not specified, the       |
   |               |         | number of entries that may be returned  |
   |               |         | is unbounded.                           |
   +---------------+---------+-----------------------------------------+
~~~~

   For all of the query parameters, the query parameter is only allowed
   for the GET and HEAD methods on "list" and "leaf-list" data
   resources.  A "400 Bad Request" status-line MUST be returned if used
   with any other method or resource type.  The error-tag value
   "operation-not-supported" is used in this case.

   Per the conformance defined in Section 3.1 of
   {{?I-D.ietf-netconf-list-pagination}}, all of these parameters MUST be
   supported for all "config true" lists and leaf-lists, and SHOULD be
   supported for "config false" lists and leaf-lists.  A server MAY
   disable the support for some or all "config false" lists, as
   described in Section 3.3 of {{!I-D.ietf-netconf-list-pagination}}.

###  The "limit" Query Parameter

   The "limit" query parameter corresponds to the "limit" parameter
   defined in Section 3.1.7 of {{?I-D.ietf-netconf-list-pagination}}.

   If the limit value is invalid, i.e. not an unsigned 32-bit integer
   greater than or equal to 1 or the string "unbounded", then a "400 Bad
   Request" status-line MUST be returned with the error-type value
   "application" and error-tag value "invalid-value".

###  The "offset" Query Parameter

   The "offset" query parameter corresponds to the "offset" parameter
   defined in Section 3.1.5 of {{?I-D.ietf-netconf-list-pagination}}.

   If the offset value is invalid, a "400 Bad Request" status-line MUST
   be returned with the error-type value "application" and error-tag
   value "invalid-value".

   If the offset value exceeds the number of entries in the working
   result set, then a "416 Range Not Satisfiable" status-line MUST be
   returned with the error-type value "application", error-tag value
   "invalid-value", and SHOULD also include the "offset-out-of-range"
   identity as the error-app-tag value.

###  The "cursor" Query Parameter

   The "cursor" query parameter corresponds to the "cursor" parameter
   defined in Section 3.1.6 of {{?I-D.ietf-netconf-list-pagination}}.

   If the cursor value is unknown, i.e. the key does not exist, a "404
   Not Found" status-line MUST be returned with the error-type value
   "application" and error-tag value "invalid-value", and SHOULD also
   include the "cursor-not-found" identity as error-app-tag value.

   If the "cursor" query parameter is not supported on the target node,
   then a "501 Not Implemented" status-line MUST be returned with
   error-type value "application" and error-tag value "operation-not-
   supported".

###  The "direction" Query Parameter

   The "direction" query parameter corresponds to the "direction"
   parameter defined in Section 3.1.4 of
   {{?I-D.ietf-netconf-list-pagination}}.

   If the direction value is invalid, then a "400 Bad Request" status-
   line MUST be returned with the error-type value "application" and
   error-tag value "invalid-value".

###  The "sort-by" Query Parameter

   The "sort-by" query parameter corresponds to the "sort-by" parameter
   defined in Section 3.1.2 of {{?I-D.ietf-netconf-list-pagination}}.

   If the specified node identifier is invalid, then a "400 Bad Request"
   status-line MUST be returned with the error-type value "application"
   and error-tag value "invalid-value".

###  The "locale" Query Parameter

   The "locale" query parameter corresponds to the "locale" parameter
   defined in Section 3.1.3 of {{?I-D.ietf-netconf-list-pagination}}.

   If the specified node identifier is invalid, i.e. the locale is
   unknown to the server, then a "501 Not Implemented" status-line MUST
   be returned with the error-type value "application" and error-tag
   value "invalid-value", and SHOULD also include the "locale-
   unavailable" identity as the error-app-tag value.

   If "locale" is supplied but not "sort-by", a "400 Bad Request"
   status-line MUST be returned with the error-type "application" and
   error-tag value "invalid-value".

###  The "where" Query Parameter

   The "where" query parameter corresponds to the "where" parameter
   defined in Section 3.1.1 of {{?I-D.ietf-netconf-list-pagination}}.

   Prefixes in the XPath expression MUST be YANG module names.
   If the specified XPath expression is invalid, then a "400 Bad
   Request" status-line MUST be returned with the error-type value
   "application" and error-tag value "invalid-value".

###  The "sublist-limit" Query Parameter

   The "sublist-limit" query parameter corresponds to the "sublist-
   limit" parameter defined in Section 3.2.1 of
   {{?I-D.ietf-netconf-list-pagination}}.

   If the sublist-limit value is invalid, then a "400 Bad Request"
   status-line MUST be returned with the error-type value "application"
   and error-tag value "invalid-value".

#  IANA Considerations

##  The "RESTCONF Capability URNs" Registry

   This document registers six capabilities in the RESTCONF Capability
   URNs {{!RFC8040}} maintained at https://www.iana.org/assignments/
   restconf-capability-urns/restconf-capability-urns.xhtml.  Following
   the instructions defined in Section 11.4 of {{!RFC8040}}, the below
   registrations are requested:

   All registrations are to use this document (RFC XXXX) for the
   "Reference" value.

~~~~
   Index          Capability Identifier
   --------------------------------------------------------------------
   :limit         urn:ietf:params:restconf:capability:limit:1.0
   :offset        urn:ietf:params:restconf:capability:offset:1.0
   :cursor        urn:ietf:params:restconf:capability:cursor:1.0
   :direction     urn:ietf:params:restconf:capability:direction:1.0
   :sort-by       urn:ietf:params:restconf:capability:sort-by:1.0
   :locale        urn:ietf:params:restconf:capability:locale:1.0
   :where         urn:ietf:params:restconf:capability:where:1.0
   :sublist-limit urn:ietf:params:restconf:capability:sublist-limit:1.0
~~~~

##  The "Media Types" Registry

   This document registers one media type in the "application"
   subregistry of the Media Types registry {{?RFC6838}} {{?RFC4855}}
   maintained at https://www.iana.org/assignments/media-types/media-
   types.xhtml#application.  Following the format defined in {{?RFC4855}},
   the below registration is requested:

###  Media Type "application/yang-data+xml-list"

   Type name: application

      Subtype name: yang-data+xml-list

      Required parameters: None

      Optional parameters: None

      Encoding considerations: 8-bit
         Each conceptual YANG data node is encoded according to the
         XML Encoding Rules and Canonical Format for the specific
         YANG data node type defined in {{?RFC7950}}.

      Security considerations: Security considerations related
         to the generation and consumption of RESTCONF messages
         are discussed in Section 12 of RFC 8040.  Additional
         security considerations are specific to the semantics
         of particular YANG data models.  Each YANG module is
         expected to specify security considerations for the
         YANG data defined in that module.

      Interoperability considerations: RFC XXXX specifies the
         format of conforming messages and the interpretation
         thereof.

      Published specification: RFC XXXX

      Applications that use this media type: Instance document data
         parsers used within a protocol or automation tool that
         utilize the YANG Patch data structure.

      Fragment identifier considerations: Fragment identifiers for
         this type are not defined.  All YANG data nodes are
         accessible as resources using the path in the request URI.

      Additional information:

         Deprecated alias names for this type: N/A
         Magic number(s): N/A
         File extension(s): None
         Macintosh file type code(s): "TEXT"

      Person & email address to contact for further information:
         See the Authors' Addresses section of RFC XXXX.

      Intended usage: COMMON

      Restrictions on usage: N/A

      Author: See the Authors' Addresses section of RFC XXXX.

      Change controller: Internet Engineering Task Force
         (mailto:iesg@ietf.org).

      Provisional registration? (standards tree only): no

#  Security Considerations

   This document introduces protocol operations for paging through data
   already provided by the RESTCONF protocol, and hence does not
   introduce any new security considerations.

   This document does not define a YANG module and hence there are no
   data modeling considerations beyond those discussed in
   {{?I-D.ietf-netconf-list-pagination}}.


--- back

# Appendix A.  Example YANG Module

   The examples within this document use the "example-social" YANG
   module defined in Appendix A.1 of {{?I-D.ietf-netconf-list-pagination}}.

# Appendix B.  Example Data Set

   The Example Data Set used by the examples is defined in Appendix A.2
   of {{?I-D.ietf-netconf-list-pagination}}.

# Appendix C.  Example Queries

##  List pagination with all query parameters

   This example mimics that Appendix A.3.9 of
   {{?I-D.ietf-netconf-list-pagination}}.  This example is presented twice,
   once using XML and again using JSON.

   XML:
   =============== NOTE: '\' line wrapping per RFC 8792 ================

~~~~
   GET /restconf/ds/ietf-datastores:operational/example-social:members/\
   member?where=//stats//joined[starts-with(timestamp,'2020')]&sort-by=\
   timestamp&direction=backwards&offset=2&limit=2&sublist-limit=1 HTTP/1.1
   Host: example.com
   Accept: application/yang-data+xml-list
~~~~

   Response from the RESTCONF server:

~~~~
   =============== NOTE: '\' line wrapping per RFC 8792 ================

   HTTP/1.1 200 OK
   Date: Thu, 26 Jan 2017 20:56:30 GMT
   Server: example-server
   Last-Modified: Thu, 26 Jan 2017 20:55:30 GMT
   Content-Type: application/yang-data+xml-list
~~~~

~~~~
{::include-fold ./examples/example-social.xml}
~~~~

   JSON:

~~~~
   =============== NOTE: '\' line wrapping per RFC 8792 ================
   GET /restconf/ds/ietf-datastores:running/example-social:members/memb\
   er?where=//stats//joined[starts-with(timestamp,'2020')]&sort-by=time\
   stamp&direction=backwards&offset=2&limit=2&sublist-limit=1 HTTP/1.1
   Host: example.com
   Accept: application/yang-data+json
~~~~

   Response from the RESTCONF server:

~~~~
   HTTP/1.1 200 OK
   Date: Thu, 26 Jan 2017 20:56:30 GMT
   Server: example-server
   Last-Modified: Thu, 26 Jan 2017 20:55:30 GMT
   Content-Type: application/yang-data+json
~~~~

~~~~
   {
     "example-social:member": [
       {
         "@": {
           "ietf-list-pagination:remaining": 1,
           "ietf-list-pagination:locale": "en_US"
         },
         "member-id": "eric",
         "email-address": "eric@example.com",
         "password": "$0$1543",
         "avatar": "BASE64VALUE=",
         "tagline": "Go to bed with dreams; wake up with purpose.",
         "following": ["alice"],
         "posts": {
           "post": [
             {
               "@": {
                 "ietf-list-pagination:remaining": 2,
                 "ietf-list-pagination:locale": "en_US"
               },
               "timestamp": "2020-09-17T18:02:04Z",
               "title": "Son, brother, husband, father",
               "body": "What's your story?"
             }
           ]
         },
         "favorites": {
           "bits": ["two"],
           "@example-social:bits": [
             {
               "ietf-list-pagination:remaining": 2,
               "ietf-list-pagination:locale": "en_US"
             }
           ]
         },
         "stats": {
           "joined": "2020-09-17T19:38:32Z",
           "membership-level": "pro",
           "last-activity": "2020-09-17T18:02:04Z"
         }
       },
       {
         "member-id": "bob",
         "email-address": "bob@example.com",
         "password": "$0$1543",
         "avatar": "BASE64VALUE=",
         "tagline": "Here and now, like never before.",
         "posts": {
           "post": [
             {
               "@": {
                 "ietf-list-pagination:remaining": 2,
                 "ietf-list-pagination:locale": "en_US"
               },
               "timestamp": "2020-08-14T03:32:25Z",
               "body": "Just got in."
             }
           ]
         },
         "favorites": {
           "decimal64-numbers": ["3.14159"],
           "@example-social:decimal64-numbers": [
             {
               "ietf-list-pagination:remaining": 1,
               "ietf-list-pagination:locale": "en_US"
             }
           ]
         },
         "stats": {
           "joined": "2020-08-14T03:30:00Z",
           "membership-level": "standard",
           "last-activity": "2020-08-14T03:34:30Z"
         }
       }
     ]
   }
~~~~


# Acknowledgments
{:numbered="false"}

   This work has benefited from the discussions of RESTCONF resource
   collection over the years, in particular,
   {{?I-D.ietf-netconf-restconf-collection}} which provides enhanced
   filtering features for the retrieval of data nodes with the GET
   method and {{?I-D.zheng-netconf-fragmentation}} which document large
   size data handling challenge.  The authors would like to thank the
   following for lively discussions on list (ordered by first name):
   Andy Bierman, Martin Björklund, Robert Varga, and Rob Wilton.

