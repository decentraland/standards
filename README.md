# Decentraland standards

* [Conventions](#conventions)
* [Introduction](#introduction)
* [Types of contributions](#types-of-contributions)
  * [Technical proposals](#technical-proposals)
  * [Pull Request](#types-of-contributions)
  * [Issue](#issue)
  * [Opinions](#opinions)
* [Style rules](#style-rules)
  * [Filename and Title](#filename-and-title)
  * [Document Status](#document-status)
  * [Maintainer](#maintainer)
  * [Conventions](#conventions)
  * [Introduction](#introduction)
  * [Related Documents](#related-documents)
  * [External content](#external-content)
  * [Further reading](#further-reading)
  * [References](#references)

## Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL
NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and
"OPTIONAL" in this document are to be interpreted as described in
[RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).

## Introduction

At Decentraland, we put a lot of effort into improving both the Software itself and the
Software Process. The pillars of the Software Process are the standards we define to organize our work.
This document describes contribution and style guides about how to write those standards.

## Types of contributions

### Pull Request

The preferred type of contribution is a Github's pull request. Pull request to the master branch from your own fork or from a local branch if you have permissions (the branch will be deleted on merge).

### Technical proposals

We opened a channel to propose technical specifciations. Those MAY affect the product itself or our the roadmap itself.
To contribute with a technical specification you shall follow the guidelines of the [proposals repository](https://github.com/decentraland/proposals).

### Issue

Issues are other very appreciated kind of contributions.

### Opinions

For opinions or open discussion feel free to join the [chat](https://chat.decentraland.org).

## Style rules

The following are the Style rules that Decentraland's standard documents must follow.

### Filename and Title

Every Standard document MUST have a title related with the
filename.

The filename MUST be enough to understand what content can be expected.

The filename MUST not contain spaces, hyphens should be used instead.

The filename MUST only contain lowercase alphanumeric characters or hyphen.

### Document Status

Every Standard document MUST contain right after the title
a line specifying the document status:

```markdown
**Document Status**: *Draft*.  
```
The possible status are:

- *Draft*. Work in progress.
- *Standard*. An Standard a specification or set of guidelines that, after extensive consensus-building, has received the endorsement of the Decentraland team.

The line should end with two white spaces to provoke a [line break](http://markdown-guide.readthedocs.io/en/latest/basics.html#line-return) without starting a new paragraph. This will be useful for the next topic, the Maintainer.

### Maintainer

Every Standard document MUST contain right after the [document status](#document-status) a line specifying the document maintainer.
The maintainer should be specified with an address specification following the [RFC2822](https://tools.ietf.org/html/rfc2822#section-3.4) standard.
The `angle-addr` should be wrapped in a link link in the [MAILTO](https://tools.ietf.org/html/rfc3696#section-4.3) format described in RFC3696.

```markdown
**Maintainer**: Agustin Mendez &lt;[agus@decentraland.org](mailto:agus@decentraland.org)&gt;.
```

The maintainer has the responsibility of coordinating issues, pull requests and any other kind
of feedback, plus to maintain the standard itself, by applying proactively changes to maintain it updated.
In order to maintain coherence in the big picture, the maintainer should be someone participating
in the architectural coordination process.

### Conventions

Every Standard document MUST follow the [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt) and MUST contain a section following this style:

```markdown
## Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL
NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and
"OPTIONAL" in this document are to be interpreted as described in
[RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).
```

### Introduction

Every Standard document MUST have as fist section an introductory section following this style:

```markdown
## Introduction

At Decentraland, we put a lot of effort into improving both the Software itself and the
Software Process. The pillars of the Software Process are the standards we define to organize our work.
This document describes style guides about how to write those standards.
```

### Related Documents

Documents that have a relationship with other documents MUST include a section following this template:

```markdown
## Related Documents

To understand better the behavior of non fungible tokens refer to [ERC821](https://github.com/decentraland/erc821) documentation.
```

### External content

Every document that contains external content must follow the content with a link to the references section labeled with the related reference:

```markdown
### Avoid installing unnecessary packages

In order to reduce complexity, dependencies, file sizes, and build times, you
should avoid installing extra or unnecessary packages just because they might be
*nice to have*. For example, you don’t need to include a text editor in a
database image. [[1](#references)]
```

### Further reading

Standard documents MAY recommend further reading. If that is the case the following template
should be followed:

```markdown
## Further reading

- Johnson, Mark (February 1997). [Building a Better ReadMe](http://www.ingentaconnect.com/content/stc/tc/1997/00000044/00000001/art00004). Technical Communication. Society for Technical Communication.

- Preston-Werner, Tom (23 Aug 2010). [Readme Driven Development](http://tom.preston-werner.com/2010/08/23/readme-driven-development.html). Tom Preston-Werner's personal blog.

- Streb, Claire (12 Sep 2015). [All Hail the README File!](http://clairenstreb.blogspot.com/2015/09/all-hail-readme-file.html). Claire N Streb - Expert Remote Senior Software Engineer's personal blog.
```

### References

Every document containing external references must contain a *References* section following the style of the template below:

```markdown
## References

- [1](https://msdn.microsoft.com/en-us/library/ee658098.aspx ) - Microsoft, What is Software Architecture?.
```