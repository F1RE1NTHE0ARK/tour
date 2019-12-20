<template>
	<div 
	ref="father"
	class="app" 
	 @mouseup="stopDrag($event)"
	@mousemove="Dragging($event)"
	>
		<span ref="fuck"  @mousedown="startDrag($event)">鼠标位置{{x}}---{{y}}<br/>当前位置{{startX}}---{{startY}}</span>
		<!-- <div class="bottom"></div>
		<div class="back" ref="back"><p>加载中...</p></div>
		<div class="front"ref="	front"></div> -->
		<p>长{{boxH}},宽{{boxW}}</p>
	</div>
</template>

<script>
import home from './components/home.basic.vue';
import inhome from './components/inhome.vue';
class Mylazyload {
	constructor(){
		const viewport = window.innerHeight||document.documentElement.clientHeight||document.body.clientHeight;
		this.init(viewport);
	}
	init(vh){
		const imgGroup = document.querySelectorAll('IMG');
		const imgTop = []
		for(let [i,len] = [0,imgGroup.length];i<len;i++){
			imgTop[i] = imgGroup[i].getBoundingClientRect().top;
			if(imgTop[i]&&imgTop[i]<vh){
				let s = imgGroup[i].getAttribute("data-src");
				imgGroup[i].setAttribute("src",s);
				imgTop[i] = false;
			}
		}
	}
}

export default {
	data(){
		return {
			arr:[
				'./images/100x100bb(149).jpg',
				'./images/100x100bb(150).jpg',
				'./images/100x100bb(151).jpg',
				'./images/100x100bb(152).jpg',
				'./images/100x100bb(153).jpg',
				'./images/100x100bb(154).jpg',
				'./images/100x100bb(155).jpg',
				'./images/100x100bb(156).jpg',
				'./images/100x100bb(157).jpg',
				'./images/100x100bb(158).jpg',
				'./images/100x100bb(159).jpg',
				'./images/100x100bb(160).jpg',
				'./images/100x100bb(161).jpg',
				'./images/100x100bb(162).jpg',
				'./images/100x100bb(163).jpg',
				'./images/100x100bb(164).jpg',
			],
			x:0,
			y:0,
			startX:0,
			startY:0,
			boxW:0,
			boxH:0.,
			dragAbled:false,
			movingBox:{
				x:[],
				y:[]
			},
			flag:false,
			timestart:0,
			timeout:0
		}
	},
	methods:{
		dragStart(e){
			this.flag=!this.flag;
			this.$refs.father.style.cursor='move';
			this.startY = e.pageY;
			console.log('start!!')
			console.log(e)
		},
		dragEnd(e){
			this.$refs.father.style.cursor = 'default';
			this.flag=!this.flag;
			this.$refs.front.style.transition='all .8s ease-out';
			this.$refs.front.style.transform=`translateY(0px)`;
			console.log('end!!');
			setTimeout(()=>{this.$refs.back.style.transform='translateY(0px)'},1000)
		},
		dragging(e){
			if(this.flag){
				this.$refs.front.style.transition='';
				this.endY = e.pageY;
				let dragAbled=this.endY - this.startY
				if(dragAbled>0){
					let step = Math.floor(dragAbled/5);
					let mini = Math.floor(dragAbled/10);
					this.$refs.front.style.transform=`translateY(${step}px)`;
					if(mini>20){
						this.$refs.back.style.transform='translateY(20px)';
					}
					else{
						this.$refs.back.style.transform=`translateY(${mini}px)`;
					}
					
				}
				else{
					this.$refs.front.style.transition='all .8s ease-out';
					this.endY=0;
					this.$refs.front.style.transform=`translateY(${this.endY}px)`;
				
				}
			}
			else{
				this.$refs.front.style.transition='all .8s ease-out';
				this.endY=0;
				this.$refs.front.style.transform=`translateY(${this.endY}px)`;
			}
			
		},
		startDrag(e){
			this.$refs.fuck.style.transition= '';
			this.x = e.clientX;//鼠标在元素里的位置
			this.y = e.clientY;
			this.dragAbled=true;//鼠标在元素上按下才允许拖动
			this.timestart=+new Date();//开始拖动时间戳
		},
		Dragging(e){
			if(this.dragAbled){
				let moveX = e.clientX;//鼠标在盒子里的位置
				let moveY = e.clientY;
				let ele = this.$refs.fuck;
				let curX = moveX-this.x+this.startX;// 最终为位置 = 鼠标在盒子里的位置 - 鼠标在元素里的位置 + 元素初始位置
				let curY = moveY-this.y+this.startY;
				curX >=0?curX:curX = 0;
				curX > this.$refs.father.offsetWidth-ele.offsetWidth -10?curX = this.$refs.father.offsetWidth - ele.offsetWidth -10:curX;
				curY >=0?curY:curY = 0;
				curY > this.$refs.father.offsetHeight-ele.offsetHeight -10?curY = this.$refs.father.offsetHeight - ele.offsetHeight -10:curY;
				ele.style.left = curX+'px'
				ele.style.top = curY +'px'
				this.movingBox.x.push(curX);
				this.movingBox.y.push(curY);
			}
		},
		stopDrag(e){
			//计算x，y初速  终速 0
			this.timeout = +new Date();//拖动结束时间戳
			let vx = this.movingBox.x[this.movingBox.x.length-1]-this.movingBox.x[this.movingBox.x.length-2];//初速度
			let vy = this.movingBox.y[this.movingBox.y.length-1]-this.movingBox.y[this.movingBox.y.length-2];
			let step = 40;//惯性,越大越滑
			let b = 0;//当前进度
			let [xa,ya,curx,cury,finx,finy] =[];
			xa = vx/step;//x减速度
			ya = vy/step;
			this.dragAbled=false;
			let interval = this.timeout - this.timestart;//拖动间隔,过短则不触发惯性运动
			if(interval>200){
				this.startX = this.$refs.fuck.offsetLeft;
				this.startY = this.$refs.fuck.offsetTop;
				return;
			}
			window.timer1 = setInterval(()=>{
				b++;
				if(b==step){//进度100，结束惯性运动
					this.startX = this.$refs.fuck.offsetLeft;//更新起始位置
					this.startY = this.$refs.fuck.offsetTop;
					this.movingBox.x =[];
					this.movingBox.y =[];
					clearInterval(window.timer1);
					return;
				}
				curx = vx -xa*b;//速度公式
				cury = vy -ya*b;
				finx =this.$refs.fuck.offsetLeft +curx;//位置
				finy =this.$refs.fuck.offsetTop +cury;
				console.log(finx)
				if(finx<0||finy<0||finx>this.$refs.father.offsetWidth-this.$refs.fuck.offsetWidth-10||finy>this.$refs.father.offsetHeight-this.$refs.fuck.offsetHeight-10)
				{
					if(finx<0&&finy>0){
						finx = 0;
						vx=-vx;
						xa=-xa;
					}
					else if(finy<0&&finx>0){
						finy = 0;
						vy=-vy;
						ya=-ya
					}
					else if(
					finx>this.$refs.father.offsetWidth-this.$refs.fuck.offsetWidth-10&&
					finy<this.$refs.father.offsetHeight-this.$refs.fuck.offsetHeight-10
					){
						finx = this.$refs.father.offsetWidth-this.$refs.fuck.offsetWidth-10;
						vx=-vx;
						xa=-xa;
					}
					else if(
					finx<this.$refs.father.offsetWidth-this.$refs.fuck.offsetWidth-10&&
					finy>this.$refs.father.offsetHeight-this.$refs.fuck.offsetHeight-10
					){
						finy=this.$refs.father.offsetHeight-this.$refs.fuck.offsetHeight-10
						vy=-vy;
						ya=-ya
					}
					else{
						vx=-vx;
						vy=-vy;
						xa=-xa;
						ya=-ya
					}
					
					
				}
				this.$refs.fuck.style.left = finx+'px';
				this.$refs.fuck.style.top = finy +'px'
				
				
				
			},1000/60)
		},
		moveToTarget(e){
			let arr = e.target.innerHTML.slice(7,e.target.innerHTML.length).split(',');
			this.$refs.fuck.style.transition = 'all .5s ease-out';
			this.$refs.fuck.style.left = arr[0]+'px';
			this.$refs.fuck.style.top = arr[1]+'px';
			this.startX = arr[0];
			this.startY = arr[1];
		}
	},
	components:{
		home,
		inhome
	},
	mounted(){
		// let lazy = new Mylazyload();
		// window.onscroll = () => {
		// 	let lazy = new Mylazyload();
		// }
		// document.onmousedown = this.dragStart;
		// document.onmouseup = this.dragEnd;
		// document.onmousemove = this.dragging;
		this.boxW=this.$refs.father.offsetWidth;
		this.boxH=this.$refs.father.offsetHeight
	}
	
	

}
</script>
<style scoped="scoped">
	img{
		width:400px;
		border:5px solid rgba(0,0,0,.6);
		height:400px;
		display: block;
	}
	span{
		width:200px;
		height:50px;
		position: absolute;
		z-index:3;
		left:0px;
		display: block;
		color:white;
		cursor:move;
		-webkit-user-select: none;
		font-size:20px;
		top:0px;
		border-radius:10px;
		background: rgba(49, 155, 245, 0.7);	
		padding:10px;
		transform-style: preserve-3d;
	}
	

	.app{
		width:100%;
		border-radius:10px;
		background: radial-gradient(#545353,#4e4d4d,#313131,black);
		height:500px;
		position: relative;
		border:5px solid #888888;
	}
	.app p{
		display: block;
		margin:0 auto;
		color:white;
		font-size: 25px;
		width:200px;
		opacity: .6;
		line-height: 500px;
	}
	.app div{
		width:100%;
		height:300px;
	
	}
	.bedrag{
		cursor: move;
	}
	.bottom{
		position: absolute;
		z-index:-9999;
		top:0;
		background:green
	}
	.back{
		background:yellow;
		text-align: center;
		position: absolute;
		z-index:-1;
		top:0;
	}
	.front{
		background:red;
	}
</style>