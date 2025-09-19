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

TODO Introduction


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
