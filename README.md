# IP-Calculator
<!DOCTYPE html>
<html>
<head>
<title>IP Calculator</title>
<style>
body {
font-family:monospace;
font-size:16px;
}
.addr {
width:30px;
}
.result {
border-bottom: 1px solid #6a6ade;
border-right: 1px solid #6a6ade;
}
.result .label {
display:inline-block;
width:200px;
background:#aaf;
}
</style>
</head>
<body>
<h1>IP Address calculator</h1>
IP Address
<input type='text' class='addr' id='q1'> .
<input type='text' class='addr' id='q2'> .
<input type='text' class='addr' id='q3'> .
<input type='text' class='addr' id='q4'>
<br>
# of host
<input type='text' class='addr' id='hostNum'><br>
# of subnet
<input type='text' class='addr' id='subnetNum'>
<br>
Note:
<br>
Netmask will be automatically guessed using #of host first
<br>
# of subnet isn't really used here as the netmask is already guessed from # of host
<br>
<button onclick='calculate();'>Calculate</button>
<hr>
<div class='result'>
<span class=label>IP Address :</span>
<span class=value id=resIP></span>
</div>
<div class='result'>
<span class=label>Subnet mask :</span>
<span class=value id=resMask></span>
</div>
<div class='result'>
<span class=label>Subnet Id (CIDR) :</span>
<span class=value id=resSubnetId></span>
</div>
<div class='result'>
<span class=label>Net Address :</span>
<span class=value id=resNet></span>
</div>
<div class='result'>
<span class=label>Broadcast Address :</span>
<span class=value id=resBC></span>
</div>
<div class='result'>
<span class=label>Standard Class :</span>
<span class=value id=resClass></span>
</div>
<div class='result'>
<span class=label>Important Block :</span>
<span class=value id=resImportantBlock></span>
</div>
<div class='result'>
<span class=label>Range :</span>
<span class=value id=resRange></span>
</div>
<div class='result'>
<span class=label>IP Binary :</span>
<span class=value id=resBinIP></span>
</div>
<div class='result'>
<span class=label>Mask Binary :</span>
<span class=value id=resBinMask></span>
</div>
<div class='result'>
<span class=label>Net Address Binary :</span>
<span class=value id=resBinNet></span>
</div>
<div class='result'>
<span class=label>BC Address Binary :</span>
<span class=value id=resBinBC></span>
</div>
<div class='result'>
<span class=label>Max # of Subnet :</span>
<span class=value id=resMaxNet></span>
</div>
<div class='result'>
<span class=label>Max # of Host :</span>
<span class=value id=resMaxHost></span>
</div>
<div style=font-size:11px;><a href="http://asudahlah.com">By. Gemul Cybermujahidz</a></div>
</body>
<script type='text/javascript'>
function calculate(){
//get values from input box
var q1=document.getElementById('q1').value;
var q2=document.getElementById('q2').value;
var q3=document.getElementById('q3').value;
var q4=document.getElementById('q4').value;
//var cidr=document.getElementById('cidr').value;
var netNum=document.getElementById('subnetNum').value;
var hostNum=document.getElementById('hostNum').value;

//guessing netmask by # of host
var hostNumDbg=0;
for(var i=32;i>=0;i--){
if(hostNum >= Math.pow(2,i)){
//hostNumDbg=Math.pow(2,i+1);
hostNumDbg=32-(i+1);
break;
}
}
var cidr=hostNumDbg;


//validate input value
if(
(q1>=0 && q1<=255) &&
(q2>=0 && q2<=255) &&
(q3>=0 && q3<=255) &&
(q4>=0 && q4<=255) &&
(cidr>=0 && cidr<=32)
){
//display IP address
document.getElementById('resIP').innerHTML=q1 + "." + q2 + "." + q3 + "." + q4;

//get IP Address binaries
var ipBin={};
ipBin[1]=String("00000000"+parseInt(q1,10).toString(2)).slice(-8);
ipBin[2]=String("00000000"+parseInt(q2,10).toString(2)).slice(-8);

 

ipBin[3]=String("00000000"+parseInt(q3,10).toString(2)).slice(-8);
ipBin[4]=String("00000000"+parseInt(q4,10).toString(2)).slice(-8);

//decide standart class

var standartClass="";
if(q1<=126) {
standartClass="A";
}else if (q1==127) {
standartClass="loopback IP"
}else if (q1>=128 && q1<=191) {
standartClass="B";
}else if (q1>=192 && q1<=223) {
standartClass="C";
}else if (q1>=224 && q1<=239) {
standartClass="D (Multicast Address)";
}else if (q1>=240 && q1<=225) {
standartClass="E (Experimental)";
}else {
standartClass="Out of range";
}

//netmask
var mask=cidr;
var importantBlock=Math.ceil(mask/8);
var importantBlockBinary=ipBin[importantBlock];
var maskBinaryBlockCount=mask%8;
if(maskBinaryBlockCount==0)importantBlock++;
var maskBinaryBlock="";
var maskBlock="";
for(var i=1;i<=8;i++){
if(maskBinaryBlockCount>=i){
maskBinaryBlock+="1";
}else{
maskBinaryBlock+="0";
}
}
//convert binary mask block to decimal
maskBlock=parseInt(maskBinaryBlock,2);

//net & broadcast addr
var netBlockBinary="";
var bcBlockBinary="";
for(var i=1;i<=8;i++){
if(maskBinaryBlock.substr(i-1,1)=="1"){
netBlockBinary+=importantBlockBinary.substr(i-1,1);
bcBlockBinary+=importantBlockBinary.substr(i-1,1);
}else{
netBlockBinary+="0";
bcBlockBinary+="1";
}
}

//put everything together, create a string container variables
var mask="";
var maskBinary="";
var net="";
var bc="";
var netBinary="";
var bcBinary="";
var rangeA="";
var rangeB="";
//loop to put whole strings block together
for(var i=1;i<=4;i++){
if(importantBlock>i) {
//blocks before the important block.
mask+="255";
maskBinary+="11111111";
netBinary+=ipBin[i];
bcBinary+=ipBin[i];
net+=parseInt(ipBin[i],2);
bc+=parseInt(ipBin[i],2);
rangeA+=parseInt(ipBin[i],2);
rangeB+=parseInt(ipBin[i],2);
}else if (importantBlock==i) {
//the important block.
mask+=maskBlock;
maskBinary+=maskBinaryBlock;
netBinary+=netBlockBinary;
bcBinary+=bcBlockBinary;
net+=parseInt(netBlockBinary,2);
bc+=parseInt(bcBlockBinary,2);
rangeA+=(parseInt(netBlockBinary,2)+1);
rangeB+=(parseInt(bcBlockBinary,2)-1);
}else {
//block after the important block.
mask+=0;
maskBinary+="00000000";
netBinary+="00000000";
bcBinary+="11111111";
net+="0";
bc+="255";
rangeA+=0;
rangeB+=255;
}
//add . separator except the last block
if(i<4){
mask+=".";
maskBinary+=".";
netBinary+=".";
bcBinary+=".";
net+=".";
bc+=".";
rangeA+=".";
rangeB+=".";
}
}

//additional : count maximum host, maximum net and current subnets
var binaryHost="";
for(var i=(31-cidr);i>=0;i--){
binaryHost=binaryHost+"1";
}
var maxHost=parseInt(binaryHost,2);
var binarySubnet="";
for(var i=cidr;i>=0;i--){
binarySubnet=binarySubnet+"1";
}
var maxSubnet=parseInt(binarySubnet,2);
var binaryCurrentSubnetBlock="";
for(var i=maskBinaryBlockCount;i>=0;i--){
binaryCurrentSubnetBlock=binaryCurrentSubnetBlock+"1";
}
var maxCurrentSubnetBlock=parseInt(binaryCurrentSubnetBlock,2);

//write the results to the page.
document.getElementById('resMask').innerHTML=mask;
document.getElementById('resNet').innerHTML=net;
document.getElementById('resBC').innerHTML=bc;
document.getElementById('resRange').innerHTML=rangeA + " - " + rangeB;
document.getElementById('resBinIP').innerHTML=ipBin[1]+"."+ipBin[2]+"."+ipBin[3]+"."+ipBin[4];
 

document.getElementById('resBinMask').innerHTML=maskBinary;
document.getElementById('resBinNet').innerHTML=netBinary;
document.getElementById('resBinBC').innerHTML=bcBinary;
document.getElementById('resClass').innerHTML=standartClass;
document.getElementById('resSubnetId').innerHTML=cidr;
document.getElementById('resMaxHost').innerHTML=maxHost+" possible host(s) in current subnet";
document.getElementById('resMaxNet').innerHTML=maxSubnet+" of total possible subnet, "+maxCurrentSubnetBlock+" possible subnet in current block";

 

document.getElementById('resImportantBlock').innerHTML=importantBlock;
}else{
alert("invalid value");
}
</script>
</html>
