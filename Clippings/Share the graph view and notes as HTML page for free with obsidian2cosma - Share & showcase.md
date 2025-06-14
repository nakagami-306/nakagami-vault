---
Title: "Share the graph view and notes as HTML page for free with obsidian2cosma - Share & showcase"
Source: "https://forum.obsidian.md/t/share-the-graph-view-and-notes-as-html-page-for-free-with-obsidian2cosma/51070"
ClippedDate: 2025-06-10
Tags:
  - "clippings"
---
[Plexus](https://forum.obsidian.md/u/Plexus)

[Dec 2022](https://forum.obsidian.md/t/share-the-graph-view-and-notes-as-html-page-for-free-with-obsidian2cosma/51070 "Post date")

Hello,

First of all, I suggest you support the Obsidian developers if their product works for you. I did this myself when the great Canvas feature has been released. [Obsidian Publish](https://obsidian.md/publish) offers a paid way to share your notes online with the nice graph view feature. However if you are looking for an alternative way to publish, several solutions exist.

## Obsidian Publish alternatives

- [GitLab Pages](https://about.gitlab.com/blog/2022/03/15/publishing-obsidian-notes-with-gitlab-pages/)
- [Hugo](https://blog.marco.ninja/notes/technology/obsidian/publish-obsidian-to-personal-site/)
- [Jekyll](https://www.youtube.com/watch?v=PZ7r3Agdk8M&ab_channel=ChrisJWilson)
- [Quartz](https://www.youtube.com/watch?v=ITiiuBNVue0)
- [Gatsby](https://www.youtube.com/watch?v=pm0mhkWj5ac&ab_channel=Co-x3)
- [Next.JS](https://www.youtube.com/watch?v=rKSpK1dXn4E&ab_channel=MatthewWong)
- [Obsidian2cosma](https://github.com/kevinpolisano/obsidian2cosma) (mine)

The latter not only allows you to share a sub-collection of your notes, but also the corresponding graph, unlike most of the other alternatives.

## Obsidian2cosma

[Cosma](https://cosma.graphlab.fr/en/) is an [open source app](https://github.com/graphlab-fr/cosma) which can generate such a graph view, from a directory of text files written in Markdown, in a single HTML file called the **cosmoscope**. It offers a simple way to explore, visualize and share your knowledge graph with others. *Software comes and goes, but data should last*. Plain text are **future-proof**, hence the importance of not making your notes too dependent on a particular software syntax and of being able to migrate them easily.

- Cosma uses the same syntax as [Zettlr](https://zettlr.com/), another great editor geared towards academic work, whose `[[internal links]]` relies on unique identifiers `[[id]]`, in the spirit of the [Zettelkasten method](https://docs.zettlr.com/en/academic/zkn-method/).
- Obsidian is different, as it uses the `[[filename]]` to link note files, which in my opinion is more restrictive in terms of **interoperability**.

There are at least two good reasons to convert a collection of Markdown files written in Obsidian to make it compatible with Cosma:

- To ensure that your notes will still be readable and editable by other software such as Zettlr (for interoperability and future-proof)
- To be able to **export and share all or part of your knowledge graph with Cosma**, in the form of **a single HTML page**, simultaneously displaying the notes on the one hand and the graph view on the other.

## Example: export notes and graph view from the LYT Kit

Let’s consider a famous example of Obsidian vault called [LYT-Kit](https://www.linkingyourthinking.com/download-lyt-kit).

Run [my python script](https://github.com/kevinpolisano/obsidian2cosma) `obsidian2cosma.py` on this vault, you will obtain this interactive page `cosmoscope.html` displaying notes and graph view that you can easily share and upload on your website.

[![LYT-kit-cosmoscope](https://forum.obsidian.md/uploads/default/optimized/3X/b/3/b3357b7f77578700e26cb6342d0ca84268f2b377_2_690x361.jpeg)](https://forum.obsidian.md/uploads/default/original/3X/b/3/b3357b7f77578700e26cb6342d0ca84268f2b377.jpeg "LYT-kit-cosmoscope")

This script also allows you to:

- Filter Markdown files to export based on type note or tags
- Add title and id metadata to your notes
- Convert your vault to a collection of notes [readable by Zettlr](https://forum.obsidian.md/t/how-do-you-use-obsidian-and-zettlr-with-the-same-notes/18302)
- Replace [Juggl syntax](https://juggl.io/Link+Types) of typed links in order to deal with [semantic links in Cosma](https://cosma.graphlab.fr/en/docs/cli/user-manual/#links)

- [Is there option for exporting graph-view with HTML format?](https://forum.obsidian.md/t/is-there-option-for-exporting-graph-view-with-html-format/40134/5)
- [How to Publish Obsidian Notes for Free (Next.JS / Tailwind CSS)](https://forum.obsidian.md/t/how-to-publish-obsidian-notes-for-free-next-js-tailwind-css/51021/2)
- [Search and retrieve notes. But how?](https://forum.obsidian.md/t/search-and-retrieve-notes-how-do-you-search-in-zettelkasten/65386/4)

7 months later

[uuanqin](https://forum.obsidian.md/u/uuanqin)

[Aug 2023](https://forum.obsidian.md/t/share-the-graph-view-and-notes-as-html-page-for-free-with-obsidian2cosma/51070/2 "Post date")

That’s a great tool! I forked this repository and make it easy for users of Windows and people from another country. See [uuanqin/obsidian2cosma](https://github.com/uuanqin/obsidian2cosma). Again, I appreciate you sharing this great tool with us.

3 months later

  

### Want to read more? Browse other topics in Share & showcase or view latest topics.

[Powered by Discourse](https://discourse.org/powered-by)