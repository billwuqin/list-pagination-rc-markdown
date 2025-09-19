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

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
