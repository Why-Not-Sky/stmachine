# Introduction #

STMachine is a generic state machine iterator. It is designed to process on any type of input, either be a simple character type or a complex mail object. So a parser which parses a text file can use the state machine to process the input character by character. Or a rule based system, for example, can use state machine as when Mail comes from John, publish the message in the broadcast.

STMachine can also bind to any Rule Interpreter (Interpreter to understand the rule files). Currently it provides the API for SCXML rule interpreter. To bind to any rule interpreter it will use Dependency Injection framework.


# Details #

Add your content here.  Format your content with:
  * Text in **bold** or _italic_
  * Headings, paragraphs, and lists
  * Automatic links to other wiki pages