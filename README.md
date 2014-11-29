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





drag.js
==========


/* Drag.js
 * Developed by Lapiz
 * Version 0.8
 * 30.06.14
 *
 * -Drag within the parent object.
 *
 *
 */


window.Drag = function(element, options){
    this.element = element;
    if (!this.element || !this.element.length) return;
    this.options = options ? options : {};
    this.options.axis = (this.options.axis == "x" || this.options.axis == "y") ? this.options.axis : "both";
    
    //Private Vars
    this.isTouchDown = false;
    this.downTouchPos = null;
    this.downElementPos = null;
    this.touchedElement;
    
    this.init();
}

window.Drag.prototype = {
    init:function(){
        var isTouch = this.isTouchDevice();
        var _this = this;
        this.element.css({"position":"absolute"});
        if (isTouch) {
            this.element.bind("touchstart", function(event){ _this.onTouchStart(event, $(this)); });
            $(window).bind("touchmove", function(event){ _this.onTouchMove(event); });
            $(window).bind("touchend", function(event){ _this.onTouchEnd(event); });
        }
        else {
            this.element.bind("mousedown", function(event){ _this.onTouchStart(event, $(this)); });
            $(window).bind("mousemove", function(event){ _this.onTouchMove(event); });
            $(window).bind("mouseup", function(event){ _this.onTouchEnd(event); });
        }
    },
    onTouchStart:function(event, element){
        var touches = this.touchCoordsFromEvent(event, true);
        if (touches.length == 1) {
            event.preventDefault();
            this.isTouchDown = true;
            this.downTouchPos = touches[0];
            this.downElementPos = {"x":element.position().left, "y":element.position().top};
            this.touchedElement = element;
            this.OnStart();
        }
        else this.isTouchDown = false;
    },
    onTouchMove:function(event){
        var touches = this.touchCoordsFromEvent(event, false);
        if (this.isTouchDown && touches.length == 1) {
            event.preventDefault();
            var element = this.touchedElement;
            var positionVariation = {x:0, y:0};
            var elementNewPos = {x:this.downElementPos.x, y:this.downElementPos.y};
            if (this.options.axis == "both" || this.options.axis == "x") positionVariation.x = touches[0].x - this.downTouchPos.x;
            if (this.options.axis == "both" || this.options.axis == "y") positionVariation.y = touches[0].y - this.downTouchPos.y;
            var parentElement = element.parent();
            var maxX = parentElement.width()-element.outerWidth(true);
            var maxY = parentElement.height()-element.outerHeight(true);
            elementNewPos.x += positionVariation.x;
            elementNewPos.y += positionVariation.y;
            if (elementNewPos.x < 0) elementNewPos.x = 0;
            else if (elementNewPos.x > maxX) elementNewPos.x = maxX;
            if (elementNewPos.y < 0) elementNewPos.y = 0;
            else if (elementNewPos.y > maxY) elementNewPos.y = maxY;
            if (this.options.axis == "both") element.css({"left":elementNewPos.x+"px", "top":elementNewPos.y+"px"});
            else if (this.options.axis == "x") element.css({"left":elementNewPos.x+"px"});
            else if (this.options.axis == "y") element.css({"top":elementNewPos.y+"px"});
            this.OnDrag();
        }
    },
    onTouchEnd:function(event){
        this.isTouchDown = false;
        this.OnEnd();
    },
    OnStart:function(){ },
    OnEnd:function(){ },
    OnDrag:function(){ },
    touchCoordsFromEvent:function(event, isDown){
        var touches = [];
        if (isDown) {
            if (this.isTouchDevice())
                for (var tInd = 0; tInd < event.originalEvent.touches.length; tInd++)
                    touches[tInd] = { x: event.originalEvent.touches[tInd].pageX, y: event.originalEvent.touches[tInd].pageY };
            else touches[0] = { x: event.pageX, y: event.pageY };
        }
        else{
            if (this.isTouchDevice())
                for (var tInd = 0; tInd < event.originalEvent.changedTouches.length; tInd++)
                    touches[tInd] = { x: event.originalEvent.changedTouches[tInd].pageX, y: event.originalEvent.changedTouches[tInd].pageY };
            else touches[0] = { x: event.pageX, y: event.pageY };
        }
        return touches;
    },
    isTouchDevice:function(){
        return "ontouchstart" in document;
    }
}






