<!doctype html>
<!--
Copyright 2017-2020 JellyWare Inc. All Rights Reserved.
-->
<html>
	<head>
		<meta charset="utf-8">
		<meta http-equiv="X-UA-Compatible" content="IE=edge">
		<meta name="description" content="BlueJelly">
		<meta name="viewport" content="width=640, maximum-scale=1.0, user-scalable=yes">
		<title>分光センサ DEMO</title>
		<link href="https://fonts.googleapis.com/css?family=Lato:100,300,400,700,900" rel="stylesheet" type="text/css">
		<link rel="stylesheet" href="style.css">


		<script type="text/javascript" src="bluejelly.js"></script>
		<script type="text/javascript" src="./smoothie.js"></script>
	</head>

<body>
<div class="container">
	<div class="title margin">
		<font color="blue"> <h1><p id="title" style="color:blue;">分光Sensor DEMO</p></h1></font>
	</div>

	<div class="contents margin">
		<button id="startNotifications" class="button" style="background-color:red;">Start</button>
		<button id="stopNotifications" class="button" style="background-color:silver;">Stop</button>
		<!--<input id="write_value" class="button" value="up" size="20">
		<button id="write" class="button">Write</button>-->
		<button id="blank" class="button" style="background-color:fuchsia;">blank</button>
		<button id="range+" class="button" style="background-color:lime;">range+</button>
		<button id="range-" class="button" style="background-color:lightgreen;">range-</button>
		<button id="pos+" class="button" style="background-color:skyblue;">pos+</button>
		<button id="pos-" class="button" style="background-color:lightblue;">pos-</button>
		<button id="Reset" class="button" style="background-color:orange;">reset</button>

		<hr>
		<div id="svg">GRAPH AREA</div>
		<hr>

		<span id="data_box0"> </span>
		<span>　</span>
		<span id="data_box1"> </span>
		<span>　</span>
		<span id="data_box2"> </span>
		<span>　</span>
		<span id="data_box3"> </span>
		<span>　</span>
		<span id="data_box4"> </span>
		<span>　</span>
		<span id="data_box5"> </span>
		<span>　</span>
		<span id="data_box6"> </span>
		
		<!--<div id="device_name"> </div>
		<div id="uuid_name"> </div>
		
		<div id="status"> </div>-->

	</div>
	<!--<div class="footer margin">
		For more information, see <a href="https://jellyware.jp/kurage" target="_blank">jellyware.jp</a> and <a href="https://github.com/electricbaka/bluejelly" target="_blank">GitHub</a> !
	</div>-->
</div>


<script>
//--------------------------------------------------
//Global変数
//--------------------------------------------------
//BlueJellyのインスタンス生成
const ble = new BlueJelly();
const ble2 = new BlueJelly();

//TimeSeriesのインスタンス生成
const ble_data = new TimeSeries();



//-------------------------------------------------
//smoothie.js
//-------------------------------------------------
function createTimeline() {
	const chart = new SmoothieChart({
	millisPerPixel: 20,
	grid: {
		fillStyle: '#ff8319',
		strokeStyle: '#ffffff',
		millisPerLine: 800
	},
	maxValue: 5000,
	minValue: 0
	});
	chart.addTimeSeries(ble_data, {
		strokeStyle: 'rgba(255, 255, 255, 1)',
		fillStyle: 'rgba(255, 255, 255, 0.2)',
		lineWidth: 4
	});
	chart.streamTo(document.getElementById("chart"), 500);
}


//--------------------------------------------------
//ロード時の処理
//--------------------------------------------------
window.onload = function () {
	//UUIDの設定
	ble.setUUID("UUID1","dd5f7232-1560-4792-953d-0b2015f15340","8796fa1b-986d-419a-8f84-137710a2354f");
	ble2.setUUID("UUID1","dd5f7232-1560-4792-953d-0b2015f15340","1e630bfc-08ca-44c0-a7c5-58dae380884d");
	//smoothie.js
	//createTimeline();
}


//--------------------------------------------------
//Scan後の処理
//--------------------------------------------------
ble.onScan = function (deviceName) {
	//document.getElementById('device_name').innerHTML = deviceName;
	document.getElementById('status').innerHTML = "found device!";
}


//--------------------------------------------------
//ConnectGATT後の処理
//--------------------------------------------------
ble.onConnectGATT = function (uuid) {
	console.log('> connected GATT!');

	//document.getElementById('uuid_name').innerHTML = uuid;
	//document.getElementById('status').innerHTML = "connected GATT!";
}


//--------------------------------------------------
//Read後の処理：得られたデータの表示など行う
//--------------------------------------------------
ble.onRead = function (data, uuid){
	let Toukaritu1;
	let Toukaritu2;
	let Toukaritu3;
	let i;
	
	var data_display = new Array('data_box0','data_box1','data_box2','data_box3','data_box4','data_box5','data_box6');

	//フォーマットに従って値を取得
	let value = "";
	var Sensor_val = new Array(7);
	var Sensor_val2 = new Array(7);
	

	for(i = 0; i < data.byteLength; i++){
		value = value + String.fromCharCode(data.getInt8(i));
	}

	//数値化
	let array_atai =value.split(',');
	
	for(i=0;i<7;i++){
		Sensor_val[i] = Number(array_atai[i]);
	}
	
	for(i=0;i<7;i++){
		Sensor_val2[i] = Sensor_val[i].toString().padStart(4,'0');
	}
	
	//HTMLにデータを表示
	for(i=0;i<7;i++){
		document.getElementById(data_display[i]).innerHTML = Sensor_val2[i];
	}


	if(button==1){ 
		for(i=0;i<7;i++){
			data_blank_table[i]=Number(array_atai[i]);
		}
		button=2;
	}else if(button==2){ 
		for(i=0;i<7;i++){
			Toukaritu1 = Number(array_atai[i]);
			Toukaritu2 = data_blank_table[i];
			Toukaritu3 = Toukaritu1 / Toukaritu2;
			Toukaritu3 *=100;
			data_table2[i]=Toukaritu3;
		}
		
		
		Toukaritu1 = array_atai[0];
		Toukaritu2 = data_blank_table[0];
		Toukaritu3 = (Toukaritu1/Toukaritu2);
		Toukaritu3 *= 100;
      
		if(Toukaritu3>Siki1){
			zairyou=""; 
		}else if(Toukaritu3>Siki2){
			zairyou="ポリカーボネート"; 
		}else if(Toukaritu3>Siki3){
			zairyou="アクリル";
		}
      
		//グラフへ反映
		//ble_data.append(new Date().getTime(), value);
		Create_grapf();
		
		Pre_zairyou=zairyou;
	}
}


//--------------------------------------------------
//Write後の処理
//--------------------------------------------------
ble2.onWrite = function(uuid){
  //document.getElementById('uuid_name').innerHTML = uuid;
  //document.getElementById('status').innerHTML = "written data"
}




//--------------------------------------------------
//Start Notify後の処理
//--------------------------------------------------
ble.onStartNotify = function(uuid){
  console.log('> Start Notify!');

  //document.getElementById('uuid_name').innerHTML = uuid;
  //document.getElementById('status').innerHTML = "started Notify";
}


//--------------------------------------------------
//Stop Notify後の処理
//--------------------------------------------------
ble.onStopNotify = function(uuid){
  console.log('> Stop Notify!');

  //document.getElementById('uuid_name').innerHTML = uuid;
  //document.getElementById('status').innerHTML = "stopped Notify";
}


//-------------------------------------------------
//ボタンが押された時のイベント登録
//--------------------------------------------------
document.getElementById('startNotifications').addEventListener('click', function() {
      ble.startNotify('UUID1');
});

document.getElementById('stopNotifications').addEventListener('click', function() {
      ble.stopNotify('UUID1');
});

/*
document.getElementById('write').addEventListener('click', function() {
  //フォーマットに従って値を変換
  const textEncoder = new TextEncoder();
  const text_data = document.getElementById('write_value').value;
  const text_data_encoded = textEncoder.encode(text_data + '\n');

  //write
  ble2.write('UUID1', text_data_encoded);
});
*/

document.getElementById('blank').addEventListener('click', function() {
	button=1;
});

document.getElementById('pos+').addEventListener('click', function() {
	if(Min_val>0){
      Max_val-=5;
      Min_val-=5;
    }
});

document.getElementById('range+').addEventListener('click', function() {
	if((Max_val-Min_val)>20){
	  Min_val+=5;
	}
});

document.getElementById('range-').addEventListener('click', function() {
if((Max_val-Min_val)<120){
          Min_val-=5;
        }
});

document.getElementById('pos-').addEventListener('click', function() {
	if(Max_val<120){
	  Max_val+=5;
	  Min_val+=5;
	}
});

document.getElementById('Reset').addEventListener('click', function() {
	Max_val = 120;
	Min_val = 0;
});







var data_table1 = new Array(7);
var data_table2 = new Array(7);
var data_blank_table = new Array(7);
let button=0;
let zairyou="";
let Pre_zairyou="";
let Siki1 = 90;
let Siki2 = 80;
let Siki3 = 65;
let screen_w = 600;
let screen_h = 400;
let Max_val = 120;
let Min_val = 0;





function Create_grapf() {
	
	let i=0;
	let ii=0;
	var plot_color = new Array('red', 'blue', 'yellow' ,'green','purple','pink','navy','teal','lime','aqua','fuchsia','maroon','gray','silver','skyblue');
	var Hachou = new Array('1400','1500','1600','1700','1800','1900','2000','2100');
	

	let display_text="<svg xmlns='http://www.w3.org/2000/svg' version='1.1' height='" + screen_h + "' width='" + screen_w + "' viewBox='-50 -60 700 540' class='SvgFrame'>";
	
	//外枠
	
	/*display_text = display_text + "<line x1='0' y1='0' x2='" + screen_w + "' y2='0' style='stroke:black;stroke-width:1' />";
	display_text = display_text + "<line x1='0' y1='" + screen_h + "' x2='" + screen_w + "' y2='" + screen_h + "' style='stroke:black;stroke-width:1' />";
	display_text = display_text + "<line x1='0' y1='0' x2='0' y2='" + screen_h + "' style='stroke:black;stroke-width:1' />";
	display_text = display_text + "<line x1='" + screen_w + "' y1='0' x2='" + screen_w + "' y2='" + screen_h + "' style='stroke:black;stroke-width:1' />";
	*/
	display_text = display_text + "<rect x='0' y1='0' width='"+screen_w+"' height='"+screen_h+"' fill='white'  style='stroke:gray;stroke-width:1' />"
	
	//横目盛り線
	for(i=1;i<=9;i++){
		display_text = display_text + "<line x1='0' y1='" + screen_h/10*i + "' x2='" +  screen_w + "' y2='" + screen_h/10*i + "' style='stroke:gray;stroke-width:1' />";
	}

	//縦目盛り線
	for(i=1;i<=8;i++){
		display_text = display_text + "<line x1='" + screen_w/8*i + "' y1='0' x2='" + (screen_w/8*i) + "' y2='" + screen_h + "' style='stroke:gray;stroke-width:1' />";
	}

	//横目盛り
	for(i=1;i<=7;i++){	
		display_text = display_text + "<text x='" + screen_w/8*i + "' y='"+(screen_h+30)+"' font-size='20' text-anchor='middle' stroke-width='1'>"+Hachou[i-1]+"</text>"
	}
	
	//縦目盛り
	for(i=0;i<=10;i++){
        display_text = display_text + "<text x='-5' y='"+ (screen_h/10*i+10) +"' font-size='20' text-anchor='end' stroke-width='1'>"+(Max_val-(Max_val-Min_val)/10*i)+"</text>"
    }
    
	
	//##########　材料の表示　##########
	if(Pre_zairyou==zairyou){
		display_text = display_text + "<text x='50' y='-30' font-size='30' fill='red' text-anchor='start' stroke-width='1'>"+ zairyou +"</text>"
	}


	//##########　X軸のタイトル表示　##########
	display_text = display_text + "<text x='"+screen_w/10*5+"' y='"+(screen_h+70)+"' font-size='25' text-anchor='middle' stroke-width='1'>波長（nm）</text>"
	

	//##########　Y軸のタイトル表示　##########
	display_text = display_text + "<text x='"+(-1*screen_h/2)+"' y='-60' font-size='25' text-anchor='middle' stroke-width='1' transform='rotate(-90,0,0)'>透過率 (%) </text>"
 	


	x1 = (screen_w / 8);
	x2=0;
    y1=0;
    y2=0;
    y3=0;

	for(i=0;i<6;i++){
      x2 = x1 + screen_w / 8;
      y3 = data_table2[i];
      y3 = y3 - Min_val;
      y3 = y3 / (Max_val - Min_val);
      y3 = y3 * screen_h;
      y1 = y3;
      y1 = screen_h - y1;
      
      if(y1>(screen_h)) y1= screen_h;
      if(y1<0) y1=0;
      
      y3 = data_table2[i + 1];
      y3 = y3 - Min_val;
      y3 = y3 / (Max_val - Min_val);
      y3 = y3 * screen_h;
      y2 = y3;
      y2 = screen_h - y2;
      y2 = y2;
      
      if(y2>(screen_h)) y2= screen_h;
      if(y2<0) y2=0;
      
      display_text = display_text + "<line x1='" + x1 + "' y1='" + y1 + "' x2='" + x2 + "' y2='" + y2 + "' style='stroke:"+ plot_color[ii] +";stroke-width:2' />";
      
	  display_text = display_text + "<circle cx='" + x1 + "' cy='" + y1 + "' r='10' fill='"+plot_color[ii]+"' />";


      x1 = x1 + screen_w / 8;
    }
    
    display_text = display_text + "<circle cx='" + x2 + "' cy='" + y2 + "' r='10' fill='"+plot_color[ii]+"' />";
    
    display_text += "</svg>"
    document.getElementById("svg").innerHTML =  display_text;


}

</script>
</body>
</html>
