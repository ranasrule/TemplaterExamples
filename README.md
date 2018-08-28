[documentation]: http://templater.info/

# Reporting for JVM and .NET

Templater is a **reporting library** which can be **easily integrated into third party apps** as advanced reporting engine.
It works by binding provided data with specified template.
Main goal is to provide customization options outside of development, so that reports/invoices/quotes and various other type of documents can be made to look as customers needs them.

Library has a minimal interface for providing data and various extension points for exposing specialized features not available in the library.
This way same code is reused across all document generation, instead of having document being generated by writing special code just for that document.

These examples show various usages of the library.

Additional [documentation] is available from the official website.

## How Templater works

Templater works by analyzing provided *docx/xlsx/csv* document for **tags**. 
Tags are snippets of text written in either **[[tag]]** or a **{{tag}}** format. 
Tags can have metadata which can be used for various customization purposes, such as formatting, verbalization and others.

Tags placed in a resizable part of the document can be duplicated by Templater when paired with a collection input.
**Major difference from other templating solution is that repeating of a collection is implicit from tag definition instead of explicit by using for loop constructs.** 
An example of this is a row in a table, list, page, sheet or the entire document.

Templater uses documents prepared in Word/Excel/some other editor, which allows for very good looking documents, without the need for writing code. 
This works great in most cases, although Templater must infer intention for the behavior from the structure of the document/tags.

Templater has minimal API for interaction:

 * high level *process* method
 * low level *replace*, *resize* and *clone*

and a builder API for customization.

Usage of Templater mostly consists from passing existing model to high level API.

## Data types

Most data types are processed by Templater with conversion to string. 
In Excel some types are converted into native Excel formats, such as numbers and dates.

Collections are usually processed by duplicating context which encapsulates all tags matching that collection.

Templater will use reflection to analyze classes, but it can work also on dynamic data types such as dictionary/map. 

Templater has special behavior for several data types, such as:

 * [image](Beginner/ImageExample) - injects image into the document
 * [nested array/DataTable](Beginner/DynamicResize) - invokes dynamic resize feature of the Templater 
 * [XML](Intermediate/DocxImport) - can inject XML as-is

and several others (DateTime, multiline strings, ...).

## Resizing

Low level `resize(tags, count)` API is used for duplicating part of the document. 
It can work for simple cases, such as single row in a table, but can be used for more complex ones:

 * [multiple rows](Intermediate/SharedCollection) - tags can span multiple rows, which will cause multi-row copying
 * [nested elements](Intermediate/ListsAndTables) - tags can span multiple tables/lists which can be used for cloning complex document layouts
 * [formula rewriting](Intermediate/Formulas) - as formulas are moved around and copied, their expressions will be adjusted accordingly
 * [row/column styles](Intermediate/WordTables) - if possible styles will be maintained during horizontal or vertical resizing (horizontal resizing requires **horizontal-resize** metadata)
 * [pushdown/pushright](Beginner/PushDownExample) - elements bellow/right of tags area will be moved according to builtin rules
 * [merge cells/named ranges/tables](Beginner/NamedRange) - influence push rules by extending the affected region
 * [XML binding](Advanced/XmlBinding) - custom XML will be changed/updated when bound in Word

## Examples

This repository contains various examples covering some of the features:

 * [understanding basics](Beginner/README.md)
 * [plugins and nontrivial documents](Intermediate/README.md)
 * [complex examples](Advanced/README.md)

## Extensibility

Templater has several extensibility points. 
Various plugins are embedded into library, while others can be registered during library initialization.

Examples:

 * [verbalize number into words](Intermediate/CollapseRegion)
 * [customize tag pattern](Intermediate/QuestionnairePlugin)
 * [custom reflection processing](Intermediate/AlternativeProperty)
 * [overriding bultin plugins](Intermediate/BoolOverride)
 * [custom type processing](Intermediate/QuestionnairePlugin)

## FAQ

 ***Q***: How can I inject text with special color/bolding instead of plain (or pre-formatted) text into Templater?  
 ***A***: You need to use XML to inject rich text into document (and format it appropriately to the target document format).

 ***Q***: Can I convert *docx/xlsx* to PDF?  
 ***A***: Templater does not support converting documents to PDF. If MS Office can't be used to do PDF conversion next best thing (which supports most features) is to set up headless LibreOffice and do [PDF conversion via it](Advanced/TemplaterServer/src/main/java/hr/ngs/templater/TemplaterServer.java#L178). Almost all non MS solution have some pixel perfect issues :(

 ***Q***: Is this just a fancy mail-merge library?  
 ***A***: Not at all. Templater can create really complex documents, but you might need to have a good knowledge of Word/Excel to create such complex documents.

 ***Q***: How does Templater compare to [Microsoft OOXML SDK](https://docs.microsoft.com/en-us/office/open-xml/open-xml-sdk)?  
 ***A***: OOXML SDK is a low level abstraction over Office document formats, while Templater is a high level abstraction over populating documents with data. Templater goal is to avoid writing code for creating reports, but instead bind provided data with an existing template. 
