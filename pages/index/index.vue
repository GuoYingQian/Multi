<template>
	
	<view class="uni-padding-wrap uni-common-mt">
		<view class="uni-flex uni-row">
			<view class="lang" :class="{activelang: curlang==='ch'}" style="-webkit-flex: 1;flex: 1;" @tap="changeLang('ch')">中文</view>
			<view class="lang" :class="{activelang: curlang==='en'}" style="-webkit-flex: 1;flex: 1;" @tap="changeLang('en')">English</view>
			<view class="lang" :class="{activelang: curlang==='fr'}" style="-webkit-flex: 1;flex: 1;" @tap="changeLang('fr')">Français</view>
		</view>
		<view class="uni-flex uni-row" style="-webkit-justify-content: space-between;justify-content: space-between;">
			<button :class="['btn', calcumulti?'checked':'unchecked']" @tap="chooseType('multi')">a x b</button>
			<button :class="['btn', calcumulti?'unchecked':'checked']" @tap="chooseType('posneg')">a - (-b)</button>
		</view>
		<view class="uni-flex uni-row" style="-webkit-justify-content: space-between;justify-content: space-between;">
			<text class="showtime text" @tap="stopTimer">{{showtime}}</text>
			<text class="currentitem text" v-show="isdoing">{{currentitem}}</text>
		</view>
		<view class="uni-flex uni-row" style="-webkit-justify-content: center;justify-content:baseline;">
			<image class="logo" @tap="startQues" :src='imagesrc'></image>
		</view>
		<!-- <view>
			<image class="logo" @tap="startQues" :src='imagesrc'></image>
		</view> -->
		<view class="uni-flex uni-row" style="-webkit-justify-content: center;justify-content: center;">
			<text class="text">{{expression}}</text>
		</view>
		<view class="input uni-flex uni-row" ref="answer" v-show="isdoing" style="-webkit-justify-content: center;justify-content: center;">
			<input class="text" style="-webkit-justify-content: center;justify-content: center;" v-model="answer" :placeholder="inputtooltip" @focus="needInput(true)" />
		</view>
		<view class="uni-flex uni-row" v-show="showresultmsg">
			<view class="text">{{resultmessage}}</view>
		</view>
		<view v-show="isdoing">
			<view class="uni-flex uni-row">
				<button class="keyitem" @tap="keyIn('7')">7</button>
				<button class="keyitem" @tap="keyIn('8')">8</button>
				<button class="keyitem" @tap="keyIn('9')">9</button>
				<button class="keyitem imagekey" @tap="keyIn('D')">←</button>
			</view>
			<view class="uni-flex uni-row">
				<button class="keyitem" @tap="keyIn('4')">4</button>
				<button class="keyitem" @tap="keyIn('5')">5</button>
				<button class="keyitem" @tap="keyIn('6')">6</button>
				<button class="keyitem" @tap="keyIn('0')">0</button>
			</view>
			<view class="uni-flex uni-row">
				<button class="keyitem" @tap="keyIn('1')">1</button>
				<button class="keyitem" @tap="keyIn('2')">2</button>
				<button class="keyitem" @tap="keyIn('3')">3</button>
				<button class="keyitem" @tap="keyIn('-')">-</button>
			</view>
			
			<view class="uni-flex uni-row">
				<button class="keyitem imagekey" @tap="keyIn('E')">{{entertext}}</button>
			</view>

			<!-- <view class="keymain uni-flex uni-column">
				<view class="uni-flex uni-row">
					<button class="keyitem" style="-webkit-flex: 1;flex: 1;" @tap="keyIn('7')">7</button>
					<button class="keyitem" style="-webkit-flex: 1;flex: 1;" @tap="keyIn('8')">8</button>
					<button class="keyitem" style="-webkit-flex: 1;flex: 1;" @tap="keyIn('9')">9</button>
				</view>
				<view class="uni-flex uni-row">
					<button class="keyitem" style="-webkit-flex: 1;flex: 1;" @tap="keyIn('4')">4</button>
					<button class="keyitem" style="-webkit-flex: 1;flex: 1;" @tap="keyIn('5')">5</button>
					<button class="keyitem" style="-webkit-flex: 1;flex: 1;" @tap="keyIn('6')">6</button>
				</view>
				<view class="uni-flex uni-row">
					<button class="keyitem" style="-webkit-flex: 1;flex: 1;" @tap="keyIn('1')">1</button>
					<button class="keyitem" style="-webkit-flex: 1;flex: 1;" @tap="keyIn('2')">2</button>
					<button class="keyitem" style="-webkit-flex: 1;flex: 1;" @tap="keyIn('3')">3</button>
				</view>
				<view class="uni-flex uni-row">
					<button class="keyitem" style="-webkit-flex: 1;flex: 1;" @tap="keyIn('-')">-</button>
					<button class="keyitem" style="-webkit-flex: 1;flex: 1;" @tap="keyIn('0')">0</button>
					
				</view>
			</view>
			<view class="keyenter uni-column">
				<button class="keyitem" @tap="keyIn('D')">D</button>
				<button class="keyitem" @tap="keyIn('E')">E</button>
			</view> -->
		</view>
	</view>
	
</template>

<script>
	export default {
		data() {
			return {
				title: 'hello',
				imagesrc: '/static/welcome.png',
				expression: '',
				result: 0,
				answer: '',
				calcumulti: false,
				isdoing: false,
				showresultmsg: false,
				
				timesecond: 0,
				time: null,
				
				minmulti:0,
				maxmulti:9,
				minposneg:-20,
				maxposneg:20,
				
				itemsize:5,
				itemdone:0,
				
				curlang: 'ch',
				res: {
					"ch" : {
					hintmessage: '点击开始',
					inputtooltip: '输入答案',
					resultmessage: 'Good, you have finished the exersize',
					entertext: '确认',
					},
					"en" : {
					hintmessage: 'Tap to start',
					inputtooltip: 'input the answer',
					resultmessage: 'Good, you have finished the exersize',
					entertext: 'Enter',
					},
					"fr" : {
					hintmessage: 'Tap to start French',
					inputtooltip: 'input the answer French',
					resultmessage: 'Good, you have finished the exersize',
					entertext: 'Entrer',
					},
				},
			};
		},
		
		computed:{
			hintmessage(){
				return this.res[this.curlang].hintmessage
			},
			inputtooltip(){
				return this.res[this.curlang].inputtooltip
			},
			resultmessage(){
				return this.res[this.curlang].resultmessage
			},
			entertext(){
				return this.res[this.curlang].entertext
			},
			showtime(){
				return ('0' + Math.floor(this.timesecond / 60)).slice(-2) + ' : ' + ('0' + this.timesecond % 60).slice(-2)
			},
			currentitem(){
				return (this.itemdone+1) + ' / ' + this.itemsize 
			}
		},
		
		onLoad() {
		},
		
		methods: {
			initcontent(){
				this.expression = ''
				this.result = 0
				this.answer = ''
				this.itemdone = 0
				this.keyvisible = false
				this.isdoing = false
				
				this.stopTimer()
				this.timesecond = 0
				this.imagesrc = '/static/welcome.png'
			},
			
			beginDo(){
				this.showresultmsg = false
				this.isdoing = true
				this.imagesrc = '/static/thinking.png'
			},
			
			endDo(){
				this.isdoing = false
				this.showresultmsg = true
				this.imagesrc = '/static/welcome.png'
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
				if(this.calcumulti === true){
					this.getMulti();
				}else{
					this.getPosNeg();
				}
				this.answer = '';
			},
			
			startQues(){
				this.initcontent()
				
				this.stopTimer()
				this.timesecond = 0
				this.getNewExpression()
				this.startTimer()
				
				this.beginDo()
			},
			
			chooseType(type){
				this.calcumulti = ('multi' === type)
				this.initcontent();
			},
			
			changeLang(lang){
				this.curlang = lang
			},
			
			needInput(need){
				this.keyvisible=need
			},
			
			keyIn(key){
				switch(key)
				{
					case 'D':
						this.answer=this.answer.slice(0, this.answer.length-1)
						break
					case '-':
						if(this.answer === '') this.answer = key
						break
					case 'E':
						if(this.answer == this.result){
							this.itemdone++
							this.imagesrc = '/static/correctanswer.png'
							setTimeout(()=>{
								this.imagesrc = (this.isdoing ? '/static/thinking.png' : '/static/welcome.png')},1000)
							if(this.itemdone < this.itemsize){
								this.getNewExpression()
							}
							else{
								this.stopTimer()
								this.endDo()
								this.initcontent()
							}	
						}
						else{
							this.answer=''
							this.imagesrc = '/static/wronganswer.png'
							setTimeout(()=>{this.imagesrc = '/static/thinking.png'},1000)
						}
						this.$refs.answer.$el.focus()
						break
					default:
						this.answer += key
				}
			},
			
			startTimer(){
				this.timer = setInterval(()=>{this.timesecond++}, 1000)
			},
			
			stopTimer(){
				clearInterval(this.timer)
				this.timer=null
			},
		},
	}
</script>

<style>
	.uni-row{
		margin-top: 15rpx;
	}
	
	
	.lang{
		padding-bottom: 5rpx; 
		text-align: center;
		font-size: medium;
	}
	
	.activelang{
		border-bottom: solid;
	}
	
	.text{
		font-size:large;
	}
	
	.btn{
		height: 70rpx;
		width: 240rpx;
		line-height: 70rpx;
		border-radius: 5000rpx;
		font-size: large;
	}
	
	.checked {
		background-color:#808080;
	}
	
	.unchecked {
		border: 3upx solid #808080;
	}

	.input {
		border-bottom-style: solid;
		text-align: center;
	}
	
	.keyitem{
		-webkit-flex: 1;
		flex: 1;
	}
	
	.logo{
		height: 230rpx;
	}
	
</style>
