---
layout: post
title:  "Return a file stream from a REST controller in Spring"
date:   2015-08-14 13:00:00
author: Shaun Donnelly
author_avatar: https://pbs.twimg.com/profile_images/587287617928110081/gXMbalSp_200x200.jpg
categories: java spring rest file stream
---

If you're using Spring Boot's REST controllers for an application, your controller methods typically look like this:


{% highlight java %}
@RequestMapping(value="", method=RequestMethod.GET, produces=MediaType.APPLICATION_JSON_VALUE)
public @ResponseBody ResponseEntity<String> getSomeResource() {
	return new ResponseEntity<String>("your response here", HttpStatus.OK);
}
{% endhighlight %}

Simple enough. Request comes in, JSON is returned on the response. But what if you want to return a file stream in your response?

It's actually pretty easy, it's just not very well documented. Just remove the `produces` block from your `@RequestMapping` annotation, and the `@ResponseBody` annotation from the method signature. Also, add a response as an argument to the method.

Finally, in the method copy an input stream to the response's output stream:

{% highlight java %}
@RequestMapping(value="download", method=RequestMethod.GET)
public void getDownload(HttpServletResponse response) {

    // Get your file stream from wherever.
	InputStream myStream = someClass.returnFile();

	// Set the content type and attachment header.
	response.addHeader("Content-disposition", "attachment;filename=myfilename.txt");
	response.setContentType("txt/plain");

	// Copy the stream to the response's output stream.
	IOUtils.copy(myStream, response.getOutputStream());
	response.flushBuffer();
}
{% endhighlight %}



