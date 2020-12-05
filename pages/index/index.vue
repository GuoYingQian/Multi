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
		<view class="infos">
			<text class="currentitem" v-show="isdoing">{{currentitem}}</text>
			<text class="showtime" @tap="stopTimer">{{showtime}}</text>
		</view>
		<view class="messages">
			<image class="logo" @tap="startQues" :src='imagesrc'></image>
			<text class="message">{{hintmessage}}</text>
		</view>
		<view class="text-area">
			<text class="expression">{{expression}}</text>
		</view>
		<view class="input" ref="answer" v-show="isdoing">
			<input v-model="answer" :placeholder="inputtooltip" @focus="needInput(true)" />
		</view>
		<view class="resultmessage" v-show="showresultmsg">
			<view>{{resultmessage}}</view>
		</view>
		<view class="keyboard" v-show="isdoing">
			<view class="keyrow">
				<button class="keyitem" @tap="keyIn('7')">7</button>
				<button class="keyitem" @tap="keyIn('8')">8</button>
				<button class="keyitem" @tap="keyIn('9')">9</button>
			</view>
			<view class="keyrow">
				<button class="keyitem" @tap="keyIn('4')">4</button>
				<button class="keyitem" @tap="keyIn('5')">5</button>
				<button class="keyitem" @tap="keyIn('6')">6</button>
			</view>
			<view class="keyrow">
				<button class="keyitem" @tap="keyIn('1')">1</button>
				<button class="keyitem" @tap="keyIn('2')">2</button>
				<button class="keyitem" @tap="keyIn('3')">3</button>
			</view>
			<view class="keyrow">
				<button class="keyitem" @tap="keyIn('-')">-</button>
				<button class="keyitem" @tap="keyIn('0')">0</button>
				<button class="keyitem" @tap="keyIn('D')">D</button>
			</view>
			<button class="keyitem" @tap="keyIn('E')">E</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'hello',
				imagesrc: '/static/logo.png',
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
					hintmessage: 'Tap to start Chinese',
					inputtooltip: 'input the answer Chinese',
					resultmessage: 'Good, you have finished the exersize',
					},
					"en" : {
					hintmessage: 'Tap to start English',
					inputtooltip: 'input the answer English',
					resultmessage: 'Good, you have finished the exersize',
					},
					"fr" : {
					hintmessage: 'Tap to start French',
					inputtooltip: 'input the answer French',
					resultmessage: 'Good, you have finished the exersize',
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
			},
			
			beginDo(){
				this.showresultmsg = false
				this.isdoing = true
			},
			
			endDo(){
				this.isdoing = false
				this.showresultmsg = true
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
							if(this.itemdone < this.itemsize){
								this.getNewExpression()
							}
							else{
								this.stopTimer()
								this.initcontent()
								this.endDo()
							}	
						}
						else{
							this.answer=''
							this.imagesrc = '/static/logo1.png'
							setTimeout(()=>{this.imagesrc = '/static/logo.png'},1000)
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
	
	.keyboard{
		display: flex;
		flex-direction: column;
	}
	
	.keyrow{
		display: flex;
		flex-direction: row;
	}
	
	.currentitem{
		margin-right: 100upx;
	}
</style>
