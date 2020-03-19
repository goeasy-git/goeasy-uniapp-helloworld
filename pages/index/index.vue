<template>
	<view class="content">
		<view class="header">
			<view class="title1">GoEasy示例</view>
			<view class="title2">Hello world</view>
		</view>
		<view class="send-box">
			<input v-model="message" />
			<text @click="sendMessage()">发送</text>
		</view>
		<view class="message-content">
			<view class="message-text" v-for="(msg, index) in messages" :key="index">{{msg}}</view>
		</view>
	</view>
	
</template>

<script>	
	export default {
		data() {
			return {
				message: "",//发送的消息内容
				messages:[], //接收到的消息列表
			}
		},
		created(){
			var self = this;
			//订阅接收消息的通道
			this.$goEasy.subscribe({
				channel: "my_channel",
				onMessage: function (message) {					
					var msgContent = message.content;
					var formattedTime = new Date(message.time).formatDate("hh:mm");
					var msg = formattedTime+ " " +msgContent;
					//保存消息
					self.messages.unshift(msg);	
				}
			});
		},
		onLoad() {		
		},
		methods: {		
			//发送消息
			sendMessage() {
				var self = this;
				this.$goEasy.publish({
					channel: "my_channel",
					message: self.message,
					onSuccess:function(){
						self.message=''; //清空发送消息内容
						console.log("send message success");
					},
					onFailed:function(error){
						console.log("send message error: ", JSON.stringify(error));
					}
				});
			}
		}
	}
</script>

<style>
	.content{
		margin: 0 20px;
		font-family: Source Han Sans CN;
	}
	.header{
		margin-top:79px;
		display: flex;
		flex-direction: column;
		align-items: center;
		color:#D02129;
	}
	.header .title1{
		height: 25px;
		line-height: 25px;
		font-size:24px;
	}
	.header .title2{
		height:25px;
		line-height: 25px;
		font-size:28px;
		font-weight: bold;
		margin-top:15px;
	}
	.send-box{
		display: flex;
		flex-direction: row;
		align-items: center;
		height: 40px;
		margin-top: 32px;
		margin-bottom: 32px;
	}
	.send-box input{
		width:85%;
		background: #EFEFEF;
		border:1px solid #C8C7CC;
		border-radius: 6px;
		padding: 8px;
	}
	.send-box text{
		width:15%;
		color:#D02129;
		margin-left:9px;
		font-size:15px;
		height: 23px;
		width: 34px;
	}
	.message-content{
		min-height: 350px;
		backgroud: #FFFFFF;
		border: 1px solid #DADADA;
		margin-top:26px;
	}
	.message-text{
		padding: 4px 11px;
	}
</style>
