<template>
	<view class="content">
		<view class="langs">
			<text :class="classlang_ch" @tap="changeLang('ch')">中文</text>
			<text :class="classlang_en" @tap="changeLang('en')">English</text>
			<text :class="classlang_fr" @tap="changeLang('fr')">Français</text>
		</view>
		<view class="btns">
			<button :class="classmulti" @tap="chooseType('multi')">5 x 8</button>
			<button :class="classposneg" @tap="chooseType('posneg')">21 - (-3)</button>
		</view>
		<view class="messages">
			<image class="logo" @tap="getNewExpression" :src='imagesrc'></image>
			<text class="message">{{hintmessage}}</text>
		</view>
		<view class="text-area">
			<text class="expression">{{expression}}</text>
		</view>
		<view class="input">
			<input v-model="answer" :placeholder="inputtooltip" @keyup="handleKeyUp"/>
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
				classmulti: 'btn unchecked',
				classposneg: 'btn checked',
				curlang: 'ch',
				classlang_ch: {
					'lang': true,
					'activelang':  true
				},
				classlang_en: {
					'lang': true,
					'activelang':  false
				},
				classlang_fr: {
					'lang': true,
					'activelang':  false
				},
				hintmessage: 'Tap to start',
				inputtooltip: '',
				minmulti:0,
				maxmulti:9,
				minposneg:-20,
				maxposneg:20
			};
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
				this.inputtooltip = 'input the answer';
			},
			
			chooseType(type){
				let checked = 'btn checked'
				let unchecked = 'btn unchecked'
				
				if('multi' === type)
				{
					this.classmulti = checked
					this.classposneg = unchecked
					this.calcumulti = true
				}
				else
				{
					this.classmulti = unchecked
					this.classposneg = checked
					this.calcumulti = false
				}
				
				this.initcontent();
			},
			
			handleKeyUp(){
				console.log('hello')
				console.log(this.answer, typeof this.answer)
			},
			changeLang(lang){
				//console.log(lang)
				this.curlang = lang
				this.classlang_ch.activelang = false
				this.classlang_en.activelang = false
				this.classlang_fr.activelang = false
				switch(lang){
					case 'ch':
					this.classlang_ch.activelang = true
					break
					
					case 'en':
					this.classlang_en.activelang = true
					break
					
					case 'fr':
					this.classlang_fr.activelang = true
					break
					
				}
			}
		}
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
		width: 150upx;
		margin: 50upx;
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
