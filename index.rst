=======================================
Sphinx and ReStructured Text Evaluation
=======================================

Hey everbody,

this is a writeup of my experiences with Sphinx and ReST, based on two use cases:

* Maintaining of the FLOW3 Documentation to ReST
* Writing my diploma thesis with Sphinx / ReST

Generally, we have had very good success in using Sphinx and Restructured Text, and I'd fully recommend using it for most other TYPO3 Subprojects, or other documentation. In this posting, I'll explain the reasons behind this in detail.

Just one note: All what I write here *reflects my personal opinion*; and although I think that my fellow FLOW3 team members agree with me in most of the points, I did not ask them (yet) for their explicit opinion.

History
=======

.. Note:: This is all my personal opinion ;)

Writing documentation always was somehow "hard" inside the TYPO3 Project. When we used *OpenOffice*, it was hard to produce well-looking and readable documentation; the documentation rendering on http://typo3.org is somehow hard to predict and is not very legible. This was IMHO mainly due to our custom-baked conversion process from OpenOffice to DocBook to HTML, which was not well-maintained.

While it was easy for the average user to write OpenOffice documentation, it was difficult for collaboratively maintained documents like the TSRef. This was because the SXW format is a binary format, so we could not use our standard diff / patch / gerrit toolchain for reviewing documentation changes.

That, among others, was one of the reasons to move away from OpenOffice to a more standardized format for documentation, which is future-proof and will handle the growth of the TYPO3 community well.

*DocBook* set out to create the standard in technical documentation publishing. They created an XML based format and a rendering toolchain based on XSL Transformations. We have worked with DocBook for quite some documentation, and I personally have set up the rendering toolchain quite a few times, both for clients and for my work. I also wrote my "Großer Beleg" at university using DocBook.

Being an XML based format, there is a rich toolchain for processing these documents. There are editors available; we mostly used `XMLMind XMLEditor <http://www.xmlmind.com/xmleditor/>`_. For conversion to output formats, we usually used custom bash or PHP scripts, which ran the multi-step conversion.

For my "Großer Beleg", I used `dblatex <http://dblatex.sourceforge.net>`_ which makes rendering DocBook -> LaTeX -> PDF a lot easier and less error-prone.

Still, there are numerous difficulties with DocBook:

* Although it is plain text, it is practically unreadable with a normal text editor, as it is very verbose and contains many many tags.
* When using XMLEditor, a plain-text diff is unreadable, as XMLEditor automatically wraps long lines.
* When one extends DocBook, one essentially has to "fork" it, again making it more difficult to use XMLEditor etc. The main
* While there are numerous solutions to cross-linking between documents (OLinks etc), they make the build process considerably more complex.

However, the main problem in my opinion was the following: While we thought a lot about how we can adjust DocBook for the needs of the TYPO3 project, we somehow forgot that documentation writing must be *fun* and *easy* for contributors, as that's the only chance they will do it IMHO. I mean, we are mostly programmers, and that's why to most of us (myself included), documentation writing is a *necessary evil*.

After having expressed my thoughts about DocBook, I'll now go deeper into my experiences with ReST and Sphinx.

Writing Experience
==================

My first impression of using ReST was, that it was really *easy*. You don't need to remember a whole bunch of XML tags, keyboard shortcuts of your XML editor, etc. You only need to fire up a text editor, and write something. Then, with a simple command, you'll get a preview in your browser showing you the produced markup.

There are quite some neat things in ReST: For example, you when using "such quotes", they are automatically convertes into opening and closing quotes. When you write two or three dashes (-), you get an en- and em-dash like this: -- ---.

For bold markup, just wrap your text in *stars*. The only thing which needed some time to get accustomed to was the syntax for code, which is the use of ``two backticks``.

In order to write code blocks, just end the last sentence with two double-colons, and indent the code::

	$this->myCode();

With the correct setup, you even get automatic syntax highlighting for code :-)

I won't show more ReST features (you can look them up in the Sphinx website); I just gave some examples so you can get a better feeling of it.

Also, the Sphinx / ReST converter gives much feedback to the user if the user used some unsupported syntax etc.

So, to sum it up, writing ReST is much much easier than writing DocBook. Rens, one of our team members, once said "I've never thought that writing documentation can actually be fun" - and that's also my feeling about it.

Extensibility
=============

In my opinion, the main benefit of ReST vs other formats (Markdown, Textile, ...) is that they have thought a lot about extensibility. There is a syntax available for creating custom code-blocks and custom inline markup, and with a little bit of python code, we can extend ReST.

We could use this for example to create custom, semantic markup for *TypoScript* or *Fluid*.

Graphviz + UML support
----------------------

There is an additional "Schmankerl" which I just discovered a few days ago for my diploma thesis: There exists an extension for Graphviz and for PlantUML, so the following code actually creates graphs and UML diagrams:

.. graphviz::

	digraph d {
		a -> b
		b -> c
		a -> c
	}

.. uml::

	class AbstractSomething {
	}

	class Foo {
		String bar
		baz()
	}
	class Greeter {
	}

	AbstractSomething <|-- Foo
	Foo --> Greeter

That, I think, makes a lot easier to illustrate certain concepts with a well-written diagram; and we can also define a common look-and-feel for diagrams.

Cross-Linking
=============

Crosslinking between documents is easily possible by an extension called "intersphinx". When rendering a Sphinx document, an index of all cross-reference targets is automatically generated. When wanting to link to a certain document, one needs to *import* this document under a prefix, and then use this prefix in links. As an example, in order to link to the *Python* documentation, one needs to define the following mapping::

	intersphinx_mapping = {'python': ('http://docs.python.org/3.2', None)}

Now, when using ``python:comparisons`` as link, we are linking to the target ``comparisons`` in the Python manual.

Thus, the only thing we need to implement linking between documents are stable and well-thought URLs to our (rendered) documents.

Output Formats
==============

Sphinx supports quite some output formats, among them HTML, PDF, EPub, ..... Here we'll only focus on HTML and PDF.

The generation of the generated HTML pages can be easily adjusted, as it is using a simple template engine for the markup, quite similar to Fluid.

For generating PDF, there are two possibilities: One with a direct conversion, the other one with LaTeX in between. I did not try the direct conversion yet. The conversion via LaTeX can be triggered with a simple command, and is giving reasonable and well-looking output.

Translating ReST documents
==========================

While translation of ReST documents had to be done manually, without tool support, for quite some time, this has changed last year thanks to a GSoC project which integrates Sphinx and ReST: http://gsoc.robertlehmann.de/making-ponies-fly/

So, basically, we can use Pootle to translate our ReST documents.

I have not yet tried this toolchain, so I don't know yet how stable it is or how well it works.

Build chain
===========

So, you might wonder how to get these nice features? I'll show you how to install Sphinx. Just follow the instructions of the Sphinx site http://sphinx.pocoo.org/ (which is unfortunately offline at the time of this writing)...

You might have guessed, that the mail you are reading is in fact a Sphinx / ReST document ;-)

After installing sphinx, here is what I did to create this document::

	mkdir ReST-summary
	cd ReST-summary
	sphinx-quickstart # answer all questions with the default

Now, to build this document, you can use::

	make html
	make latexpdf # if you have LaTeX installed

For PlantUML and Graphviz support, I needed to adjust the generated config a little; you can see the changes I did there in https://github.com/skurfuerst/ReST-summary/commit/db4d0d7e8ba989efab4a18d08ed4a19758be13f9#diff-0

Closing Notes
=============

So, my suggestion would definitely be to use ReST and Sphinx instead of DocBook in all of the TYPO3 documentation projects.

It really feels like a great tool, making documentation writing painless and fun. It gives you the *flow* back (at least me ;-) ).

I'd appreciate any feedback :-)

Greets,
Sebastian