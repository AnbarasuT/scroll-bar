scroll-bar
==========
//// must add area class for container


var rightScroll = $("<div id=\"scropMain_con\"><div id=\"scrollMain\"></div></div>");


/////

#scropMain_con {
position: absolute;
/* top: 10%; */
left: 100%;
width: 5%;
height: 94%;
border-top: 2px solid #063A21;
border-right: 2px solid #063A21;
border-bottom: 2px solid #063A21;
border-left: 2px solid #063A21;
background: #D4e8a6;
}

#scrollMain {
position: absolute;
top: 0%;
left: 0%;
width: 100%;
height: 30px;
border-radius: 8px;
-webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.498039);
background-color: rgba(0, 0, 0, 0.498039);
cursor: pointer;
}

//////////////////////////


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

