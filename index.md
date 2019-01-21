---
layout: default
title: Imparaveis - Because We Love What We Can Do
---
<h1>{{ page.title }}</h1>
<ul class="posts">

	{% for post in site.posts %}
		<div class="stockIframe ">
    <iframe src="{{ post.url }}" onload="autoResize(this)" height="100%" width="100"></iframe> 
</div>

<style>
   html,body { height: 100% }
   .stockIframe {  width:100%; height:100%; }
   .stockIframe iframe {  width:100%; height:100%; border:0;overflow:hidden }
</style>

<script>
   function autoResize(i) {
     var iframeHeight=
     (i).contentWindow.document.body.scrollHeight;
     (i).height=iframeHeight+20;
   }
</script>

	{% endfor %}
</ul>
