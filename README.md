# hexalated
# A game where the goal is to clear the field in the least amount of turns by clicking colored hexagon clusters

<html>
	<head>
		<script src=https://teikn-js.googlecode.com/svn-history/r23/trunk/libraries/kinetic-v3.6.0.js></script>
		<script>	
			var hexagon = [];
			var moves = 0;
			var movesCounted = "ON";
			var score = 0;
			var scoreInitial = 300;
			var scorePerMove = 10;

			function writeMessage(messageLayer, message){
				var context = messageLayer.getContext();
				messageLayer.clear();
				context.font = "18pt Calibri";
				context.fillStyle = "black";
				context.fillText(message, 10, 25);
			}


			function eliminateHexes(vOrigColor, mouseAction){
				var repeat = false;
				for (var a = 0, blackHexagon; blackHexagon = hexagon[a]; a++) {
					if (blackHexagon.hexagonOn == "OFF") {
						for (var b = 0, cHexagon; cHexagon = hexagon[b]; b++) {	
							var xCoordOffset = blackHexagon.coordY%2 == 0 ? -1 : 1;
								if (cHexagon.hexagonColor == vOrigColor) {
									if ((cHexagon.coordY == blackHexagon.coordY - 1 && cHexagon.coordX == blackHexagon.coordX + 0) ||
									(cHexagon.coordY == blackHexagon.coordY - 1 && cHexagon.coordX == blackHexagon.coordX + xCoordOffset) ||
									(cHexagon.coordY == blackHexagon.coordY + 0 && cHexagon.coordX == blackHexagon.coordX - 1) ||
									(cHexagon.coordY == blackHexagon.coordY + 0 && cHexagon.coordX == blackHexagon.coordX + 1) ||
									(cHexagon.coordY == blackHexagon.coordY + 1 && cHexagon.coordX == blackHexagon.coordX + 0) ||
									(cHexagon.coordY == blackHexagon.coordY + 1 && cHexagon.coordX == blackHexagon.coordX + xCoordOffset)) {
									switch (mouseAction) {
										case "DOWN":
											cHexagon.hexagonColor = "black";
											cHexagon.hoverOn = "OFF";
											cHexagon.hexagonOn = "OFF";
											repeat = true;
											if (movesCounted == "OFF") {
												moves++;
												movesCounted = "ON";
												score = moves * scorePerMove >= scoreInitial ? 0 : scoreInitial - scorePerMove * moves;
											}
										break;
										case "OVER":
											cHexagon.hexagonColor = cHexagon.hexagonColorHover;
											cHexagon.hoverOn = "ON";
											cHexagon.hexagonOn = "OFF";
											repeat = true;		      						
										break;
										case "OUT":
											for (var c = 0, hoverHexagon; hoverHexagon = hexagon[c]; c++) {
												if (hoverHexagon.hoverOn == "ON") {
													hoverHexagon.hexagonColor = hoverHexagon.hexagonOrigColor;
													hoverHexagon.hoverOn = "OFF";
													hoverHexagon.hexagonOn = "ON";
												}
											}			      						
										break;
									}
								}
							}
						}
					}
				}
				movesCounted = "ON";
				return repeat;
			}

			function clippedBackgroundImage(ctx, img, w, h, X, Y, hover){
			  var imgHeight = w / img.width * img.height;
			  
			  ctx.save(); // Save the context before clipping
			  ctx.clip(); // Clip to whatever path is on the context

			  if (hover == "ON") {
				ctx.globalAlpha = 0.4;
			  }
			  
			  if (imgHeight < h){
				ctx.fillStyle = '#000';
				ctx.fill();
			  }
			  
			  ctx.drawImage(img,X,Y,w,imgHeight);
			  ctx.restore(); // Get rid of the clipping region
			}

			window.onload = function(){
				//NewGame();
			};

			function NewGame(){
				document.getElementById("settings").style.display = 'none';

				var gridSize = parseFloat(document.getElementById("txtGridSizeID").value);//20;	
				var hexSideLength = parseFloat(document.getElementById("txtHexSizeID").value);//15;
				var hexAngleX = 45;
				var hexAngleY = 45;	
				var halfHexagonX = hexSideLength * Math.sin(hexAngleX);
				var halfHexagonY = hexSideLength * Math.cos(hexAngleY) + hexSideLength;
				var stage = new Kinetic.Stage("container", halfHexagonX * (gridSize + 1) * 2, halfHexagonY * (gridSize + 1));
				var shapesLayer = new Kinetic.Layer();
				var messageLayer = new Kinetic.Layer();
				var colors = ["red", "orange", "gold", "darkgreen", "lightblue", "mediumblue", "purple", "black"];
				var colorsHover = ["coral", "sandybrown", "yellow", "limegreen", "cyan", "dodgerblue", "mediumorchid", "black"];
				var firstTurn = true;
				var img;

				var e = document.getElementById("container");
				e.style.width = halfHexagonX * (gridSize + 1) * 2;
				e.style.height = halfHexagonY * (gridSize + 1);

				/////////////////////////////////////////// TEXTURES            
							
				var img0=new Image();
				img0.onload = function(){
					  // Now you can pass the `img` object to various functions
				};            
				img0.src="0.png";

				var img1=new Image();
				img1.onload = function(){
					  // Now you can pass the `img` object to various functions
				};            
				img1.src="1.png";
							
				var img2=new Image();
				img2.onload = function(){
					  // Now you can pass the `img` object to various functions
				};            
				img2.src="2.png";

				var img3=new Image();
				img3.onload = function(){
					  // Now you can pass the `img` object to various functions
				};            
				img3.src="3.png";

				var img4=new Image();
				img4.onload = function(){
					  // Now you can pass the `img` object to various functions
				};            
				img4.src="4.png";

				var img5=new Image();
				img5.onload = function(){
					  // Now you can pass the `img` object to various functions
				};            
				img5.src="5.png";

				var img6=new Image();
				img6.onload = function(){
					  // Now you can pass the `img` object to various functions
				};            
				img6.src="6.png";

				var imgSpace=new Image();
				imgSpace.onload = function(){
					  // Now you can pass the `img` object to various functions
				};            
				imgSpace.src="space.png";

				////////////////////////////////////////////////////

				for (var m = 0; m < gridSize; m++) {
					for (var n = 0; n < gridSize; n++) {
						(function(){
							var i = n;
							var j = m;
							var randomColor = Math.floor(Math.random() * 7);

							hexagon[m * gridSize + n] = new Kinetic.Shape(function(hexagonOrigColor, hexagonColor, hexagonColorHover, coordX, coordY, hexagonOn, hoverOn){     
								var canvas = this.getCanvas();
								var context = this.getContext();  

								//this.hexagonOn = typeof(this.hexagonOn) != 'undefined' ? this.hexagonOn : "ON";
								//this.hoverOn = typeof(this.hoverOn) != 'undefined' ? this.hoverOn : "OFF";

								if (j%2 == 0) {
									var hexStartX = halfHexagonX + halfHexagonX * 2 * i;
								}
								else {
									var hexStartX = halfHexagonX * 2 + halfHexagonX * 2 * i;
								}       
								
								var hexStartY = halfHexagonY * j;

								this.coordX = i;
								this.coordY = j;
								
								if (this.hexagonColor != "black") {
									switch (randomColor) {
										case 0:
											img = img0;
										break;
										case 1:
											img = img1;
										break;
										case 2:
											img = img2;
										break;
										case 3:
											img = img3;
										break;
										case 4:
											img = img4;
										break;
										case 5:
											img = img5;
										break;
										case 6:
											img = img6;
										break;
									}

									this.hexagonColor = colors[randomColor];
									this.hexagonColorHover = colorsHover[randomColor];
									this.hexagonOrigColor = this.hexagonColor;
								}
								else {
									this.hexagonColor = colors[7];
									this.hexagonColorHover = colorsHover[7];
								}				
										
								context.save;
								
								context.beginPath();
								context.lineWidth = 2;
								context.strokeStyle = this.hexagonOn != "OFF" ? "black" : "indigo";
								//context.fillStyle = this.hoverOn != "ON" ? this.hexagonColor : this.hexagonColorHover;
								context.moveTo(hexStartX, hexStartY);
								context.lineTo(hexStartX + hexSideLength * Math.sin(hexAngleX), hexStartY + hexSideLength * Math.cos(hexAngleY));
								context.lineTo(hexStartX + hexSideLength * Math.sin(hexAngleX), hexStartY + hexSideLength * Math.cos(hexAngleY) + hexSideLength);
								context.lineTo(hexStartX, hexStartY + hexSideLength * Math.cos(hexAngleY) * 2 + hexSideLength);
								context.lineTo(hexStartX - hexSideLength * Math.sin(hexAngleX), hexStartY + hexSideLength * Math.cos(hexAngleY) + hexSideLength);
								context.lineTo(hexStartX - hexSideLength * Math.sin(hexAngleX), hexStartY + hexSideLength * Math.cos(hexAngleY));
								context.closePath();
								
								if (this.hexagonOn != "OFF" || this.hoverOn == "ON") {
									clippedBackgroundImage(context, img, halfHexagonX * 2, halfHexagonY * 3 / 2, hexStartX - halfHexagonX, hexStartY, this.hoverOn);
								}
								else {
									clippedBackgroundImage(context, imgSpace, halfHexagonX * 2, halfHexagonY * 3 / 2, hexStartX - halfHexagonX, hexStartY, this.hoverOn);    			     
								}
								
								////shapesLayer.fill();
								context.stroke();
								context.restore();
							});

							/*
							hexagon.draggable(true);

							hexagon.on("dragstart", function(){
								hexagon.moveToTop();
								layer.draw();
							}); 
							*/

							hexagon[m * gridSize + n].on("mouseover", function(){
								document.body.style.cursor = "pointer";
										
								if (this.hexagonOn != "OFF") {
									var repeat = true;
									
									while (repeat == true) {
										repeat = eliminateHexes(this.hexagonOrigColor, "OVER");	
									}
								}
								
								shapesLayer.draw();
							});
						
							hexagon[m * gridSize + n].on("mouseout", function(){
								document.body.style.cursor = "default";
											
								//if (this.hexagonHover != "OFF") {
								var repeat = true;
								
								while (repeat == true) {
									repeat = eliminateHexes(this.hexagonOrigColor, "OUT");	
								} 
								//}                       	
								
								shapesLayer.draw();
							});
						
							hexagon[m * gridSize + n].on("mousedown", function(){
								if (firstTurn == true) {
									this.hexagonColor = "black";
									this.hexagonOn = "OFF";
									firstTurn = false;
								}                            
											
								//writeMessage(messageLayer, hexagon.indexOf(this));
												
								movesCounted = "OFF";
								
								var repeat = true;
								
								while (repeat == true) {
									repeat = eliminateHexes(this.hexagonOrigColor, "DOWN");	
								}
								
								var movesHTML = "Moves: " + moves;					
								var scoreHTML = "Score: " + score;
								
								document.getElementById("moves").innerHTML=movesHTML;
								document.getElementById("score").innerHTML=scoreHTML;

								shapesLayer.draw();
							});
										
							hexagon[m * gridSize + n].on("mouseup", function(){
								shapesLayer.draw();
							});
							 
							shapesLayer.add(hexagon[m * gridSize + n]);
						})();			
					}
				}		
				stage.add(shapesLayer);
				stage.add(messageLayer);   
			}
		</script>
		<style type="text/css"></style>
	</head>
	<body bgcolor="black">// onmousedown="return false;">
		<table border="0" align="center">
			   <tr>
				<td colspan="2">
					<p align="center">
						<font size="5" face="arial" color="aliceblue">
							Hexalated
						</font>
					</p>
				</td>
			</tr>	        
			<tr>
				<td>
					<p align="center">
						<font size="2" face="arial" color="aliceblue">
							<span id="moves">Moves: 0</span>
						</font>
					</p>
				</td>
				<td>
					<p align="center">
						<font size="2" face="arial" color="aliceblue">
							<span id="score">Score: 300</span>
						</font>
					</p>
				</td>
			</tr>
			<tr>
				<td colspan="2">
					<div id="container"></div>
				</td>
			</tr>	        
			<tr>
				<td colspan="2">
					<p align="center">
						<font size="2" face="arial" color="aliceblue">
							<span id="settings">
								Grid Size:
								<input type="text" name="txtGridSize" id="txtGridSizeID" value="20" />
								Hex Size:
								<input type="text" name="txtHexSize" id="txtHexSizeID" value="15" />
								<button onclick="NewGame();">Launch</button>
							</span>
						</font>
					</p>
				</td>
			</tr>
			<tr>
				<td colspan="2">
					<p align="right">
						<font size="2" face="arial" color="aliceblue" >
							//GM & KG © 2012
						</font>
					</p>
				</td>
			</tr>	
		</table>	
	</body>
<html>
