---
title: "Doc2vec with Gizmodo articles"
layout: post
date: 2016-01-15 22:48
image: /assets/images/markdown.jpg
headerImage: false
tag:
- visualization
- data
blog: true
author: innashteinbuk
description: 
---

<link rel="stylesheet" href="/assets/css/doc2vec_gizmodo/col.css" crossorigin="anonymous">
<script src="/assets/js/doc2vec_gizmodo/external/d3.v3.min.js" charset="utf-8"></script>
<script src="/assets/js/doc2vec_gizmodo/external/jquery-1.7.min.js" charset="utf-8"></script>
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.0/jquery.min.js"></script>
<script src="https://code.jquery.com/jquery-1.10.1.min.js"></script>
<script src="/assets/js/doc2vec_gizmodo/external/jquery-ui.min.js" charset="utf-8"></script>
<script src="/assets/js/doc2vec_gizmodo/external/three.min.js"></script>
<script src="/assets/js/doc2vec_gizmodo/Projector.js"></script>
<script src="/assets/js/doc2vec_gizmodo/TrackballControls.js"></script>
<link rel="stylesheet" href="https://ajax.googleapis.com/ajax/libs/jqueryui/1.10.3/themes/smoothness/jquery-ui.min.css">
<script src="/assets/js/doc2vec_gizmodo/BasicVis.js" type="text/javascript"></script>
<script src="/assets/js/doc2vec_gizmodo/CostLayout-worker-3D.js" type="text/javascript"></script>
<script src="/assets/js/doc2vec_gizmodo/data/top_1000.js" type="text/javascript"></script>
<script src="/assets/js/doc2vec_gizmodo/data/urls.js" type="text/javascript"></script>

As part of a Cornell Tech project with Chartbeat last semester, I used Python's Scrapy to scrape articles with all their comments off of gizmodo.com. For this blog post, I scraped gizmodo.com for 8,000 articles, trained a doc2vec model, and reduced the resulting vectors to make neat 2D and 3D visualizations. I wanted to see whether these visualizations of the reduced vectors from doc2vec would appear in clusters that "made sense".

Doc2vec, an extension of word2vec, is an unsupervised learning method that attempts to learn longer chunks of text (docs). Doc2vec uses the same one hidden layer neural network architecture from word2vec, but also takes into account whatever "doc" you are using. It uses the same context window from word2vec, but concatenates the doc vector containing those words to the words in your context window. The diagram from [Mikolov et. al](https://arxiv.org/pdf/1405.4053v2.pdf) below shows this architecture. 

<figure>
    <a href = "/assets/images/doc2vec_paper.png"><img src="/assets/images/doc2vec_paper.png" alt="image"></a>
    <!-- <figcaption><a title="from Mikolov et. al: https://arxiv.org/pdf/1405.4053v2.pdf"></a>.</figcaption> -->
</figure>

Just like word2vec, this method will learn the semantics of the text instead of just clustering documents that have the same words. I used gensim's implementation, whose documentation can be seen [here](https://radimrehurek.com/gensim/models/doc2vec.html). I used each Gizmodo articles' text as a document, and trained the doc2vec model on 8,000 documents. 

After Doc2vec, I did a couple manual checks to see if the articles that were deemed "similar" by cosine similarity (so the closer the score to 1, the closer the 2 article vectors are to each other) were somewhat related, but I noticed that most articles did not have high similarity scores. I plotted the distribution of cosine similarity scores between all articles below. We can see that scores were not high, most articles had a score of .4 or .5. .

<figure>
    <a href = "/assets/images/similarities_hist.jpg"><img src="/assets/images/similarities_hist.jpg" alt="image"></a>
    <!-- <figcaption><a title="Histogram of Cosine Similarities">Histogram of Cosine Similarities</a>.</figcaption> -->
</figure>


As an example, [Drunk Guy Arrested for Kicking a Pepper Robot](http://gizmodo.com/drunk-guy-arrested-for-kicking-a-pepper-robot-1729293319) matched with [You Could Creepily Dress Your Pepper Robot Up Like a Doll](http://gizmodo.com/you-could-creepily-dress-your-pepper-robot-up-like-a-do-1744672027) with a score of .49. Rarely articles would have higher scores like [Nikon D810 Review The Ultimate Adventure Camera](http://indefinitelywild.gizmodo.com/nikon-d810-review-the-ultimate-adventure-camera-1720304051) matching with [Sony A7S Adventure Tested Ultimate Low-Light Mirrorless](http://indefinitelywild.gizmodo.com/sony-a7s-adventure-tested-ultimate-low-light-mirrorles-1726566715) with a score of .77. The latter pair don't seem to be similar from the titles, but the articles are structured/written identically. The two robot articles were both about Pepper Robot.

I tried different methods to reduce the 300 dimensional vectors. tSNE worked best, which doesn't preserve distances like other methods such as MDS, but calculates similarities between vectors as probabilities, and then preserves those probabilities. Generally tSNE did not preserve the correct similarities, but did preserve similarities between articles that had high similarity scores. I think tSNE didn't do well on all articles because the articles don't clearly separate into categories. This is unlike say MNIST data, which does well with tSNE because there are similarities within the data and it noticeably has 10 clusters.

For the purposes of this visualization, I used only 1,000 articles. First I chose these articles randomly, but after tSNE, the visualizations looked like blobs and articles weren't next to their real 300 dimensions neighbors. So, I found each article's most similar pair, then only used the articles with the top 1,000 similarity scores. In this case, tSNE preserved the highest similarites, and actually revealed some clusters in two and three dimensions! The visualizations are shown below. To make the visualizations, I adapted code from the talented Christopher Olah's [blog post on MNIST](https://colah.github.io/posts/2014-10-Visualizing-MNIST/). 

Gizmodo articles come with meta-tags that include "keywords". I named the clusters by using the keywords that appear the most. All keywords are listed in order of their frequency (high to low). 2D keywords are listed below in their respective colors. Clustering in 3D revealed slightly different categories which are listed above the 3D graphic. 
<ul>
<li><font color = "#b48c40">Space, Science, Astronomy, Nasa, Environment</font></li>
<li><font color = "#59bf40">Reviews, Photography, Android, Smartphones, Shootingchallenge, Apple, Samsung</font></li>
<li><font color = "#bf4040">Field Guide, Android, Apps, iOS, Google, Tips, Reviews, Windows, Apple</font></li>
<li><font color = "#40bf73">Television, TV Recap, Star Wars, Movies, True Crime, Toybox</font></li>
<li><font color = "#a6bf40">Science, True Crime, Medicine, Murder, Biology, Health, Animals</font></li>
</ul>
<!-- brown -->
<!-- <div style="background-color:#b48c40; width: 20px; height: 20px; display: inline-block"></div><div style="display:inline-block"> = Space, Science, Astronomy, Nasa, Environment</div>
 --><!-- green -->
<!-- <div style="background-color:#59bf40; width: 20px; height: 20px; display: inline-block"></div><div style="display:inline-block"> = Reviews, Photography, Android, Smartphones, Shootingchallenge, Apple, Samsung</div> -->

<!-- red -->
<!-- <div style="background-color:#bf4040; width: 20px; height: 20px; display: inline-block"></div><div style="display:inline-block"> = Field Guide, Android, Apps, iOS, Google, Tips, Reviews, Windows, Apple</div> -->

<!-- bluish green -->
<!-- <div style="background-color:#40bf73; width: 20px; height: 20px; display: inline-block"></div><div style="display:inline-block"> = Television, TV Recap, Star Wars, Movies, True Crime, Toybox</div> -->

<!-- barf green -->
<!-- <div style="background-color:#a6bf40; width: 20px; height: 20px; display: inline-block"></div><div style="display:inline-block"> = Science, True Crime, Medicine, Murder, Biology, Health, Animals</div> -->
<script type="text/javascript">
var N = 1000;
var D = 2;
var ds_orig2d = new Float32Array(N*N);
var xs = first_1000_xs_2d;
for (var i = 0; i < N; i++)
for (var j = 0; j < N; j++) {
var sum = 0;
for (var d = 0; d < D; d++){
  var diff = xs[D*i + d] - xs[D*j + d];
  sum += diff*diff;
}
ds_orig2d[i + N*j] = Math.sqrt(sum);
}

var D = 3;
var ds_orig3d = new Float32Array(N*N);
var xs = first_1000_xs_3d;
for (var i = 0; i < N; i++)
for (var j = 0; j < N; j++) {
var sum = 0;
for (var d = 0; d < D; d++){
  var diff = xs[D*i + d] - xs[D*j + d];
  sum += diff*diff;
}
ds_orig3d[i + N*j] = Math.sqrt(sum);
}

function knn_points(K, i, D, ds_orig) {
  var knn = [];
  /* stupid, dumb, easy hack for testing:*/
  for (var k = 0; k < K; k++) {
    var x = null, xd = 100000;
    for (var j = 0; j < N; j++) {
      var D = ds_orig[i + N*j];
      if ( i!=j && knn.indexOf(j) == -1 && D < xd) {
        x = j;
        xd = D;
      }
    }
    knn.push(x);
  }
  return knn;
}

</script>

<script type="text/javascript">
var first_1000_ys = $.map(first_1000_ys, function(el) { return el });
var text_tooltip = new BasicVis.TextTooltip();
text_tooltip._labels = first_1000_ys;
setTimeout(function() {text_tooltip.hide();}, 3000);
</script>

<div id="twod" class="figure" style="width: 60%; margin: 0 auto; border: 1px solid black; margin-bottom: 8px;">
</div>
<!-- <br> -->
<script type="text/javascript">
setTimeout(function(){
var test = new GraphLayout("#twod", 35);
test.scatter.size(3.3);
setTimeout(function() {
  test.scatter.xrange([-11,13]);
  test.scatter.yrange([-11,13]);
  text_tooltip.bind(test.scatter.points);
}, 50);
var W = new Worker("/assets/js/doc2vec_gizmodo/CostLayout-worker-3D.js");
W.onmessage = function(e) {
  data = e.data;
  switch (data.msg) {
    case "update":
      test.sne = data.embed;
      window.requestAnimationFrame(function() { test.rerender();});
      break;
    case "ready":
      break;
  }
};
W.postMessage({cmd: "init_2d", xs: first_1000_xs_2d, N: N, D: 2});
}, 500);
</script>

<!-- <div style="background-color:#13b4ff; width: 20px; height: 20px; display: inline-block"></div><div style="display:inline-block"> = something, something else</div> -->


<div class="caption">
(Click a point to view similar articles. You can zoom in and out of the graphic.)
</div>


<div class="row">
  <div id="col-title-2d"></div>
  <div class="col-md-6" id="2d-display">
    <h4>Click a point above to view similar articles!</h4>
  </div>  
  <div class="col-md-6" id="2d-doc2vec-display"></div>  
</div>

3D keywords are listed below in their respective colors:
<ul>
<li><font color = "#b48c40">Reviews, Photography, Android, Shootingchallenge, Smartphones, Cameras, Apple</font></li>
<li><font color = "#59bf40">Television, True Crime, Star Wars, Movies, Murder</font></li>
<li><font color = "#bf4040">Science, Medicine, Biology, Cities, Environment, Transportation, Health</font></li>
<li><font color = "#40bf73">Space, Astronomy, Nasa, Earth&Space</font></li>

<li><font color = "#a6bf40">Field Guide, Android, Apps, Reviews, iOS, Windows 10, Tips, Apple (This is yellow in the graphic)</font></li>
</ul>

<div class="figure" style="width: 90%; margin: 0 auto; border: 1px solid black; padding: 5px; margin-bottom: 8px;">
<div id="threed" style="width: 100%">

</div>
</div>


<div class="caption">
(Drag your mouse above the points to see the closest articles. You can also zoom in and spin the hairball around.)
</div>

<div class="row">
  <div id="col-title-3d"></div> 
  <div class="col-md-6" id="3d-display">
    <h4>Drag your mouse above a point above to view similar articles!</h4>
  </div>  
  <div class="col-md-6" id="3d-doc2vec-display"></div>  
</div>



<script type="text/javascript">
setTimeout(function(){
var test = new BasicVis.GraphPlot3("#threed");
test.controls.reset();
test.layout();
test._animate();
/*test.point_classes = first_1000_ys;*/
test.point_classes = colors_1000_3D;
var test_wrap = new AnimationWrapper(test);

var tooltip = null;
setTimeout(function() {
  test_wrap.layout();

  test.point_event_funcs["mouseover"] = function(i) {
    /*need to put i through a filter to convert it back to the original coordinates*/

    text_tooltip.display(i);
    text_tooltip.unhide();
    var orig_closest = idx_top3[i];
    var knn = []; 
    knn = knn_points(3, i, 3, ds_orig3d);
    var title_string = '<h4> Article: <a id="article" href="' + urls[i_to_orig[i]] + '"target="_blank">' + first_1000_ys[i] + '</h4></a>';
    $('#col-title-3d').html(title_string);
    var html_string = '<h5> Closest articles in 3D: </h5>';
    html_string += '<ul>';
    for (var i = 0; i < knn.length; i++) {
      html_string += '<li><a id="article" href="' + urls[i_to_orig[knn[i]]] + '"target="_blank">' + first_1000_ys[knn[i]] + '</a></li>';  
    }
    html_string += '</ul>';

    var better_string = "";
    better_string += '<h5> Closest articles in 3D: </h5>';
    better_string += '<ul>';
    for (var r = 0; r < orig_closest.length; r++) {
      better_string += '<li><a id="article" href="' + urls[orig_closest[r]] + '"target="_blank">' + all_articles[orig_closest[r]] + '</a></li>'; 
    }
    better_string += '</ul>';
    $('#3d-display').html(html_string);
    $('#3d-doc2vec-display').html(better_string);

  };
  test.point_event_funcs["mouseout"] = function(i) {
    text_tooltip.hide();
  };
  text_tooltip.bind_move(test.s, i);
  text_tooltip.bind_click(test.s);
  
}, 50);
var W = new Worker("/assets/js/doc2vec_gizmodo/CostLayout-worker-3D.js");
W.onmessage = function(e) {
  data = e.data;
  switch (data.msg) {
    case "edges":
      test.make_points(N);
      test.make_edges(data.edges);
      break;
    case "update":
      test.position(data.embed);
      break;
    case "done":
      test_wrap.on_done();
      break;
  }
};
W.postMessage({cmd: "init", xs: first_1000_xs_3d, N: N, D: 3, cost: "graph"});
}, 500);
</script>


All my code is [here](https://github.com/innainu/blog/tree/master/gizmodo_doc2vec). The code for the scraper is [here](https://github.com/innainu/gizmodo_scraper). In the future, I'd like to see how using more data would help the performance of doc2vec and tSNE. Because I had originally built the scraper to extract comments from articles, I'd like to see whether a particular user tends to comment on articles that doc2vec deems similar. 
