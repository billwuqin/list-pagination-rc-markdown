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
    fullname: "Qin Wu"
    organization: Your Organization Here
    email: "bill.wu@huawei.com"

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

   Various examples in this document use "BASE64VALUE=" as a placeholder
   value for binary data that has been base64 encoded (per Section 9.8
   of {{!RFC7950}}).  This placeholder value is used because real base64
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
   the XML encoding", as required by Section 4.3 of {{!RFC8040}}.

   For JSON, the existing "application/yang-data+json" media type is
   sufficient, as the JSON format has built-in support for encoding
   arrays.

   The "application/yang-data+xml-list" media type is registered in
   Section 3.2.1.

##  Query Parameters

   This document extends Section 4.8 of {{!RFC8040}} to add new query
   parameters "limit", "offset", "cursor", "direction", "sort-by",
   "locale", "where", and "sublist-list".

   These six query parameters correspond to those defined in Sections
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
   |               | HEAD    | result set that should the skipped over |
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
   defined in Section 3.1.7 of [I-D.ietf-netconf-list-pagination].

   If the limit value is invalid, i.e. not an unsigned 32 bit integer
   greater than or equal to 1 or the string "unbounded", then a "400 Bad
   Request" status-line MUST be returned with the error-type value
   "application" and error-tag value "invalid-value".

###  The "offset" Query Parameter

   The "offset" query parameter corresponds to the "offset" parameter
   defined in Section 3.1.5 of [I-D.ietf-netconf-list-pagination].

   If the offset value is invalid, a "400 Bad Request" status-line MUST
   be returned with the error-type value "application" and error-tag
   value "invalid-value".

   If the offset value exceeds the number of entries in the working
   result set, then a "416 Range Not Satisfiable" status-line MUST be
   returned with the error-type value "application", error-tag value
   "invalid-value", and SHOULD also include the "offset-out-of-range"
   identity as error-app-tag value.

###  The "cursor" Query Parameter

   The "cursor" query parameter corresponds to the "cursor" parameter
   defined in Section 3.1.6 of [I-D.ietf-netconf-list-pagination].

   If the cursor value is unknown, i.e. the key does not exist, a "404
   Not Found" status-line MUST be returned with the error-type value
   "application" and error-tag value "invalid-value", and SHOULD also
   include the "cursor-not-found" identity as error-app-tag value.

   If the "cursor" query parameter is not supported on the target node,
   then a a "501 Not Implemented" status-line MUST be returned with
   error-type value "application" and error-tag value "operation-not-
   supported".

###  The "direction" Query Parameter

   The "direction" query parameter corresponds to the "direction"
   parameter defined in Section 3.1.4 of
   [I-D.ietf-netconf-list-pagination].

   If the direction value is invalid, then a "400 Bad Request" status-
   line MUST be returned with the error-type value "application" and
   error-tag value "invalid-value".

###  The "sort-by" Query Parameter

   The "sort-by" query parameter corresponds to the "sort-by" parameter
   defined in Section 3.1.2 of [I-D.ietf-netconf-list-pagination].

   If the specified node identifier is invalid, then a "400 Bad Request"
   status-line MUST be returned with the error-type value "application"
   and error-tag value "invalid-value".

###  The "locale" Query Parameter

   The "locale" query parameter corresponds to the "locale" parameter
   defined in Section 3.1.3 of [I-D.ietf-netconf-list-pagination].

   If the specified node identifier is invalid, i.e. the locale is
   unknown to the server, then a "501 Not Implemented" status-line MUST
   be returned with the error-type value "application" and error-tag
   value "invalid-value", and SHOULD also include the "locale-
   unavailable" identity in as the error-app-tag value.

   If "locale" is supplied but not "sort-by", a "400 Bad Request"
   status-line MUST be return with the error-type "application" and
   error-tag value "invalid-value".

###  The "where" Query Parameter

   The "where" query parameter corresponds to the "where" parameter
   defined in Section 3.1.1 of [I-D.ietf-netconf-list-pagination].

   Prefixes in the XPath expression MUST be YANG module names.
   If the specified XPath expression is invalid, then a "400 Bad
   Request" status-line MUST be returned with the error-type value
   "application" and error-tag value "invalid-value".

###  The "sublist-limit" Query Parameter

   The "sublist-limit" query parameter corresponds to the "sublist-
   limit" parameter defined in Section 3.2.1 of
   [I-D.ietf-netconf-list-pagination].

   If the sublist-limit value is invalid, then a "400 Bad Request"
   status-line MUST be returned with the error-type value "application"
   and error-tag value "invalid-value".
 
# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
