<template>
	<transition name="fade">
		<div class="photo-preview" v-show="dialogVisible">
			<div class="thumbnail-img-wrap">
				<span class="close" @click="closeDialog">X</span>
				<div class="thumbnail-img-container">
					<div class="thumbnail-img-list">
						<div
							class="thumbnail-img-item"
							v-for="(item, index) in previewPictureArray"
							:key="index"
							:class="initialIndex == index ? 'active' : ''"
						>
							<img :src="item.fullThumbnailPath" alt="略缩图" @click="handleSetActiveItem(index)" />
						</div>
					</div>
				</div>
			</div>
			<div class="model">
				<el-carousel
					ref="carousel"
					:arrow="arrowVisible"
					:initial-index="initialIndex"
					:autoplay="false"
					@change="handleImgChange"
					indicator-position="none"
				>
					<el-carousel-item v-for="(item, index) in previewPictureArray" :key="index">
						<div class="carousel-item_picture" @click="closeDialog">
							<img
								:src="item.fullPath"
								v-drag
								ref="drag"
								:class="{'current-big-img':isBigImg && initialIndex == index,'fit-screen-img':isFitImg }"
								@click.stop="changeImgSize(index)"
								alt="大图"
							/>
						</div>
					</el-carousel-item>
				</el-carousel>
			</div>
		</div>
	</transition>
</template>
<script>
//拖拽指令  当图片大于屏幕大小时可以拖拽
let translate;
const drag = {
	bind: function(el, binding) {
		el.onmousedown = function(e) {
			e.stopPropagation();
			//console.log("onmousedown")
			translate = false;
			let clientWidth = document.documentElement.clientWidth - 200;
			let clientHeight = document.documentElement.clientHeight;
			let imgWidth = el.offsetWidth;
			let imgHeight = el.offsetHeight;

			//console.log("imgWidth " + imgWidth, "imgHeight " + imgHeight);

			if (imgHeight <= clientHeight && imgWidth <= clientWidth) {
				return false;
			}

			let disX = e.clientX - el.offsetLeft;
			let disY = e.clientY - el.offsetTop;

			document.onmousemove = function(e) {
				translate = true;

				let L = e.clientX - disX;
				let T = e.clientY - disY;

				if (imgWidth <= clientWidth) {
					L = "auto";
				} else if (clientWidth - L > imgWidth) {
					L = clientWidth - imgWidth;
				} else if (L > 0) {
					L = 0;
				}

				if (imgHeight <= clientHeight) {
					T = "auto";
				} else if (clientHeight - T > imgHeight) {
					T = clientHeight - imgHeight;
				} else if (T > 0) {
					T = 0;
				}

				if (T == "auto") {
					el.style.top = T;
				} else {
					el.style.top = T + "px";
				}

				if (L == "auto") {
					el.style.left = L;
				} else {
					el.style.left = L + "px";
				}
			};
			document.onmouseup = function(e) {
				e.stopPropagation();
				//console.log("onmouseup")
				document.onmousemove = null;
			};
			return false;
		};
	}
};

export default {
	name: 'previewPhoto',
	directives: { drag },
	data() {
		return {
			dialogVisible: false,
			canMagnifyImg: false,
			isBigImg: false,
			isFitImg: false,
			initialIndex: 0,
			arrowVisible: "always"
		};
	},
	props: ["previewPictureArray"],
	watch: {
		previewPictureArray() {
			if (this.previewPictureArray.length <= 1) {
				this.arrowVisible = "never";
			} else {
				this.arrowVisible = "always";
			}
		}
	},
	methods: {
		//图片切换
		handleImgChange(index, oldIndex) {
			this.initialIndex = index;
			this.imgSizeLimit(index);
		},
		//击图片放大或缩小
		changeImgSize(index) {
			if (this.canMagnifyImg && !translate) {
				if (this.isFitImg) {
					this.isFitImg = false;
					this.isBigImg = true;
				} else {
					this.isFitImg = true;
					this.isBigImg = false;
				}
				let img = this.$refs.drag[index];
				img.style.top = "auto";
				img.style.left = "auto";
			}
		},
		//切换选中的图片
		handleSetActiveItem(index = 0) {
			this.dialogVisible = true;
			this.initialIndex = index;
			this.$nextTick(() => {
				this.$refs.carousel.setActiveItem(index);
			});
			this.imgSizeLimit(index);
		},
		//判断图片的大小限制
		imgSizeLimit(index) {
			this.$nextTick(() => {
				let clientWidth = document.documentElement.clientWidth - 200;
				let clientHeight = document.documentElement.clientHeight;
				let img = this.$refs.drag[index];
				let imgWidth = img.offsetWidth;
				let imgHeight = img.offsetHeight;
				//console.log("w我是imgWidth " + imgWidth, "w我是imgHeight " + imgHeight);

				if (imgHeight < clientHeight && imgWidth < clientWidth) {
					this.canMagnifyImg = false;
					this.isFitImg = false;
					this.isBigImg = false;
				} else {
					this.canMagnifyImg = true;
					this.isFitImg = true;
					this.isBigImg = false;
				}
				img.style.top = "auto";
				img.style.left = "auto";
			});
		},
		closeDialog() {
			this.dialogVisible = false;
		},
		openDialog() {
			this.dialogVisible = true;
		}
	}
};
</script>
<style lang="stylus" scoped>
.photo-preview {
	position: relative;
	z-index: 999;
	height: 900px;

	.thumbnail-img-wrap {
		position: fixed;
		top: 0;
		right: 0;
		background-color: #fff;
		width: 200px;
		height: 100%;
		padding: 50px 0 0 15px;
		overflow: hidden;

		.close {
			position: absolute;
			top: 15px;
			right: 15px;
			color: #999;
			cursor: pointer;

			&:hover {
				color: #666;
			}
		}

		.thumbnail-img-container {
			overflow: auto;
			width: 100%;
			height: 100%;
		}
	}
}

.thumbnail-img-list {
	display: flex;
	flex-flow: row wrap;
	justify-content: space-between;
	align-content: flex-start;
	width: 170px;

	.thumbnail-img-item {
		width: 80px;
		height: 80px;
		line-height: 80px;
		background-color: #eee;
		margin-bottom: 10px;
		overflow: hidden;
		display: flex;
		justify-content: center;
		align-items: center;

		img {
			height: 80px;
			width: 80px;
			object-fit: contain;
			cursor: pointer;
		}
	}

	.active {
		border: 1px solid #E60012;
	}
}

.model {
	position: fixed;
	top: 0;
	left: 0;
	right: 200px;
	bottom: 0;
	background-color: rgb(0, 0, 0);
	overflow: auto;

	img {
		position: absolute;
		z-index: 999;
	}

	.fit-screen-img {
		max-height: 100%;
		max-width: 100%;
		cursor: zoom-in;
	}

	.current-big-img {
		cursor: zoom-out;
	}
}

.model /deep/ {
	.el-carousel__container {
		height: 100vh;

		.carousel-item_picture {
			overflow: hidden;
			height: 100vh;
			background-color: #000;
			text-align: center;
			display: flex;
			justify-content: center;
			align-items: center;
			position: relative;
		}
	}
}

.fade-enter-active, .fade-leave-active {
  transition all .5s ease
}

.fade-enter, .fade-leave-to {
  opacity 0
}

</style>