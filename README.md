scroll-bar
==========
//// must add area class for container


var rightScroll = $("<div id=\"scropMain_con\"><div id=\"scrollMain\"></div></div>");


	var draggableObject = new Drag($("#scrollMain"), {axis:"y"});
	draggableObject.OnDrag = function(){
		enableReset();
		if(draggableObject.touchedElement) setScrollerProp(draggableObject.touchedElement);
	}
	


function setScrollerProp(scrollButton,readScroller) {
	try{
		var scrollbar = scrollButton.parent();
		var referenceArea = scrollbar.parent().find(".area");
		var innerArea = referenceArea.children().eq(0);
		if (innerArea.height() > referenceArea.outerHeight(true)) {
		    var hiddenArea = innerArea.height() - referenceArea.outerHeight(true);
		    var scrollBtnMaxTop = scrollbar.height()-scrollButton.outerHeight(true);
		    var posPerc = (parseFloat(scrollButton.css("top").replace("px","")))/scrollBtnMaxTop;
		    referenceArea.scrollTop(hiddenArea*posPerc);
		    if (!readScroller) {
			scrollButton.clearQueue();
			var visiblePerc = referenceArea.outerHeight(true)/innerArea.height();
			var proposedHeight = visiblePerc*scrollbar.height();
			var topAdj = (scrollButton.position().top+proposedHeight) - scrollbar.height();
			if (topAdj > 0) {
			    var newTop = scrollButton.position().top-topAdj;
			    scrollButton.animate({"top":newTop+"px"}, 100);
			}
			scrollButton.animate({"height":proposedHeight+"px"}, 100);
			var posPerc = referenceArea.scrollTop()/hiddenArea;
			scrollButton.css({"top":(scrollBtnMaxTop*posPerc)+"px"});
			if (visiblePerc >= 1) scrollButton.animate({"opacity":0},100);
			else scrollButton.animate({"opacity":1},100);
		    }
	}
	else {
		$("#scropMain_con").hide();
		scrollButton.css({"opacity":"0"});
	}}
	catch(e){}
}

