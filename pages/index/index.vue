<template>
	<view class="content">
		<view class="langs">
			<view class="lang" :class="{activelang: curlang==='ch'}" @tap="changeLang('ch')">中文</view>
			<view class="lang" :class="{activelang: curlang==='en'}" @tap="changeLang('en')">English</view>
			<view class="lang" :class="{activelang: curlang==='fr'}" @tap="changeLang('fr')">Français</view>
		</view>
		<view class="btns">
			<button :class="['btn', calcumulti?'checked':'unchecked']" @tap="chooseType('multi')">a x b</button>
			<button :class="['btn', calcumulti?'unchecked':'checked']" @tap="chooseType('posneg')">a - (-b)</button>
		</view>
		<view class="times">
			<text class="showtime" @tap="stopTimer">{{showtime}}</text>
		</view>
		<view class="messages">
			<image class="logo" @tap="startQues" :src='imagesrc'></image>
			<text class="message">{{hintmessage}}</text>
		</view>
		<view class="text-area">
			<text class="expression">{{expression}}</text>
		</view>
		<view class="input">
			<input v-model="answer" :placeholder="inputtooltip"/>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello',
				imagesrc: '/static/logo.png',
				expression: '',
				result: 0,
				answer: '',
				calcumulti: false,
				curlang: 'ch',
				res: {
					"ch" : {
					hintmessage: 'Tap to start Chinese',
					inputtooltip: 'input the answer Chinese',
					},
					"en" : {
					hintmessage: 'Tap to start English',
					inputtooltip: 'input the answer English',
					},
					"fr" : {
					hintmessage: 'Tap to start French',
					inputtooltip: 'input the answer French',
					},
				},
				timesecond: 0,
				time: null,
				minmulti:0,
				maxmulti:9,
				minposneg:-20,
				maxposneg:20,
			};
		},
		computed:{
			hintmessage(){
				return this.res[this.curlang].hintmessage
			},
			inputtooltip(){
				return this.res[this.curlang].inputtooltip
			},
			showtime(){
				return ('0' + Math.floor(this.timesecond / 60)).slice(-2) + ' : ' + ('0' + this.timesecond % 60).slice(-2)
			},
		},
		onLoad() {
		},
		
		methods: {
			initcontent(){
				this.expression = ''
				this.result = 0
				this.answer = ''
				this.inputtooltip = ''
			},
			getrandom(min, max){
				return min + Math.floor(Math.random()*(max-min+1));
			},
			
			getMulti(){
				let numa = this.getrandom(this.minmulti,this.maxmulti);
				let numb = this.getrandom(this.minmulti,this.maxmulti);
				this.expression = numa +' X ' + numb +' = ';
				this.result = numa * numb;
				
			},
			
			getPosNeg(){
				let numa = this.getrandom(this.minposneg, this.maxposneg);
				let numb = this.getrandom(this.minposneg, this.maxposneg);
				let numOp = this.getrandom(0,1);
				
				this.expression = numa + ' ' + (numOp === 0?'+':'-');
				this.expression += ' ' + (numb < 0 ? '(' : '');
				this.expression += numb;
				this.expression += ' ' + (numb < 0 ? ')' : '');
				this.expression += ' = ';
				
				this.result = numOp===0?numa+numb:numa-numb;
			},
			
			getNewExpression(){
				this.imagesrc = (this.imagesrc === '/static/logo1.png'?'/static/logo.png':'/static/logo1.png');
				if(this.calcumulti === true){
					this.getMulti();
				}else{
					this.getPosNeg();
				}
				this.answer = '';
			},
			
			startQues(){
				this.stopTimer()
				this.timesecond = 0
				this.getNewExpression()
				this.startTimer()
			},
			
			chooseType(type){
				this.calcumulti = ('multi' === type)
				this.initcontent();
			},
			
			changeLang(lang){
				this.curlang = lang
			},
			
			startTimer(){
				this.timer = setInterval(()=>{this.timesecond++}, 1000)
			},
			
			stopTimer(){
				console.log(this.timer)
			},
		},
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
		display: inline-flex;
		float: left;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
	
	.input {
		border-bottom-style: solid;
		text-align: center;
	}
	.messages_{
		display: flex;
		flex-direction: row;
	}
	.btns{
		margin-top: 20upx;
	}
	.btn {
		position: relative;
		border: 0upx;
		display: inline-flex;
		align-items: center;
		justify-content: center;
		box-sizing: border-box;
		padding: 0 30upx;
		
		
		font-size: 28upx;
		height: 64upx;
		width: 240upx;
		line-height: 1;
		text-align: center;
		text-decoration: none;
		overflow: visible;
		margin-left: initial;
		transform: translate(0upx, 0upx);
		margin-right: 20upx;
		border-radius: 5000upx;
	}
	
	.lang{
		position: relative;
		display: inline-block;
		text-align: center;
		width: 180upx;
		margin: 50upx;
		padding-bottom: 10upx;
	}
	
	.activelang{
		border-bottom: solid;
	}
	
	.checked {
		background-color:#808080;
	}
	
	.unchecked {
		border: 3upx solid #808080;
	}
	
	
</style>
