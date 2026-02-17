<template>
	<view class="container">
		<!-- Background Blobs (Simplified for Mobile) -->
		<view class="blob-container">
			<view class="blob blob-1"></view>
			<view class="blob blob-2"></view>
		</view>

		<!-- Status Bar Height -->
		<view class="status-bar" :style="{ height: statusBarHeight + 'px' }"></view>
		
		<view class="content-wrapper">
			<!-- Header -->
			<header class="header">
				<text class="header-title">Tasks</text>
				<text class="header-date">{{ currentDate }}</text>
			</header>
			
			<!-- Tabs -->
			<nav class="tabs">
				<view 
					v-for="tab in tabList" 
					:key="tab.value" 
					:class="['tab-btn', currentTab === tab.value ? 'tab-btn-active' : '']"
					@click="currentTab = tab.value"
				>
					{{ tab.label }}
				</view>
			</nav>
			
			<!-- Task List -->
			<scroll-view scroll-y class="task-list">
				<view v-if="filteredTasks.length === 0" class="empty-state">
					<text>No tasks yet</text>
				</view>
				
				<view 
					v-for="(item, index) in filteredTasks" 
					:key="item.id" 
					class="task-card-wrapper"
					@touchstart="touchStart($event, item)"
					@touchmove="touchMove($event, item)"
				>
					<view :class="['task-card', item.isDone ? 'task-done' : '']" @click="toggleTask(item)">
						<!-- Custom Checkbox -->
						<view :class="['checkbox', item.isDone ? 'checked' : '']">
							<text v-if="item.isDone" class="check-icon">✓</text>
						</view>
						<!-- Task Text -->
						<text :class="['task-text', item.isDone ? 'text-done' : '']">{{ item.content }}</text>
					</view>
					
					<!-- Delete Action (Overlays on Swipe) -->
					<view 
						class="delete-action" 
						:style="{ transform: 'translateX(' + (item.isSwiped ? '0' : '100%') + ')' }"
						@click.stop="deleteTask(item.id)"
					>
						<view class="custom-trash">
							<view class="trash-lid"></view>
							<view class="trash-body">
								<view class="trash-line"></view>
								<view class="trash-line"></view>
							</view>
						</view>
						<text class="delete-text">Delete</text>
					</view>
				</view>
				
				<!-- Spacer for footer -->
				<view style="height: 180rpx;"></view>
			</scroll-view>
		</view>
		
		<!-- Footer Input -->
		<view class="footer-input-container">
			<view class="glass-panel">
				<view class="input-wrapper">
					<text class="add-plus-icon">+</text>
					<input 
						class="main-input"
						type="text" 
						v-model="newTaskContent" 
						placeholder="Add a new task..." 
						placeholder-class="placeholder"
						@confirm="addTask"
					/>
				</view>
				<view :class="['send-btn', newTaskContent.trim() ? 'send-btn-active' : 'send-btn-disabled']" @click="addTask">
					<text class="arrow-icon">↑</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			newTaskContent: '',
			currentTab: 'todo',
			tabList: [
				{ label: 'To-Do', value: 'todo' },
				{ label: 'Done', value: 'done' },
				{ label: 'All', value: 'all' }
			],
			tasks: [],
			startX: 0,
			startY: 0,
			currentDate: '',
			statusBarHeight: 0
		}
	},
	computed: {
		filteredTasks() {
			if (this.currentTab === 'todo') return this.tasks.filter(t => !t.isDone);
			if (this.currentTab === 'done') return this.tasks.filter(t => t.isDone);
			return this.tasks;
		}
	},
	onLoad() {
		this.statusBarHeight = uni.getSystemInfoSync().statusBarHeight || 20;
		this.loadTasks();
	},
	onShow() {
		this.formatDate();
	},
	methods: {
		formatDate() {
			const options = { weekday: 'long', month: 'long', day: 'numeric' };
			this.currentDate = new Date().toLocaleDateString('en-US', options);
		},
		loadTasks() {
			const data = uni.getStorageSync('clean_tasks_v2');
			if (data) {
				this.tasks = JSON.parse(data).map(item => ({ ...item, isSwiped: false }));
			}
		},
		saveTasks() {
			uni.setStorageSync('clean_tasks_v2', JSON.stringify(this.tasks));
		},
		addTask() {
			if (!this.newTaskContent.trim()) return;
			const newTask = {
				id: Date.now(),
				content: this.newTaskContent,
				isDone: false,
				isSwiped: false
			};
			this.tasks.unshift(newTask);
			this.newTaskContent = '';
			this.saveTasks();
			if (this.currentTab === 'done') this.currentTab = 'all';
		},
		toggleTask(item) {
			item.isDone = !item.isDone;
			this.saveTasks();
		},
		deleteTask(id) {
			this.tasks = this.tasks.filter(t => t.id !== id);
			this.saveTasks();
		},
		touchStart(e, item) {
			this.startX = e.touches[0].clientX;
			this.startY = e.touches[0].clientY;
			this.tasks.forEach(t => {
				if (t.id !== item.id) t.isSwiped = false;
			});
		},
		touchMove(e, item) {
			const moveX = e.touches[0].clientX;
			const moveY = e.touches[0].clientY;
			const deltaX = moveX - this.startX;
			const deltaY = moveY - this.startY;
			
			if (Math.abs(deltaX) > Math.abs(deltaY) && Math.abs(deltaX) > 10) {
				if (deltaX < -30) {
					item.isSwiped = true;
				} else if (deltaX > 30) {
					item.isSwiped = false;
				}
			}
		}
	}
}
</script>

<style scoped>
.container {
	position: relative;
	width: 100%;
	height: 100vh;
	overflow: hidden;
}

/* Background Blobs */
.blob-container {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	pointer-events: none;
	z-index: 0;
}
.blob {
	position: absolute;
	border-radius: 50%;
	filter: blur(60px);
	opacity: 0.15;
}
.blob-1 {
	top: -10%;
	right: -10%;
	width: 400rpx;
	height: 400rpx;
	background-color: #3B82F6;
}
.blob-2 {
	top: 20%;
	left: -10%;
	width: 300rpx;
	height: 300rpx;
	background-color: #A855F7;
}

.status-bar {
	width: 100%;
	z-index: 10;
}

.content-wrapper {
	position: relative;
	z-index: 1;
	padding: 40rpx;
	display: flex;
	flex-direction: column;
	height: calc(100vh - var(--status-bar-height));
}

/* Header */
.header {
	margin-top: 40rpx;
	margin-bottom: 60rpx;
}
.header-title {
	display: block;
	font-size: 72rpx;
	font-weight: 700;
	color: var(--text-main);
	letter-spacing: -2rpx;
	margin-bottom: 8rpx; /* Added spacing between title and date */
}
.header-date {
	font-size: 36rpx;
	color: var(--text-sub);
	font-weight: 500;
}

/* Tabs */
.tabs {
	display: flex;
	margin-bottom: 60rpx;
	gap: 20rpx;
}
.tab-btn {
	padding: 20rpx 48rpx;
	border-radius: 100rpx;
	font-size: 28rpx;
	font-weight: 600;
	background-color: var(--card);
	color: var(--text-sub);
	transition: all 0.3s;
	box-shadow: 0 4rpx 16rpx rgba(0,0,0,0.05);
}
.tab-btn-active {
	background-color: var(--primary);
	color: #FFFFFF;
	box-shadow: 0 8rpx 24rpx rgba(59, 130, 246, 0.4);
}

/* Task List */
.task-list {
	flex: 1;
	height: 0; /* Force scroll-view to take flex space correctly */
}
.empty-state {
	text-align: center;
	padding-top: 100rpx;
	color: var(--text-sub);
}

.task-card-wrapper {
	margin-bottom: 24rpx;
	position: relative; /* Base for absolute positioning */
	border-radius: 32rpx;
	overflow: hidden;
}

.task-card {
	width: 100%;
	padding: 20rpx 40rpx;
	background-color: var(--card);
	border-radius: 32rpx;
	display: flex;
	align-items: center;
	box-shadow: 0 10rpx 40rpx -10rpx rgba(0,0,0,0.05);
	position: relative;
	z-index: 1; /* Stay below delete button */
}

.task-done {
	background-color: rgba(59, 130, 246, 0.05);
	box-shadow: none;
}

/* Checkbox */
.checkbox {
	width: 44rpx;
	height: 44rpx;
	border: 4rpx solid var(--primary-soft);
	border-radius: 16rpx;
	margin-right: 32rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	transition: all 0.2s;
}
.checked {
	background-color: var(--primary);
	border-color: var(--primary);
}
.check-icon {
	color: #FFFFFF;
	font-size: 24rpx;
	font-weight: bold;
}

.task-text {
	font-size: 32rpx;
	font-weight: 500;
	color: var(--text-main);
	transition: all 0.2s;
}
.text-done {
	color: var(--text-sub);
	text-decoration: line-through;
	opacity: 0.6;
}

/* Delete Action - Overlays on top */
.delete-action {
	position: absolute;
	top: 0;
	right: 0;
	width: 120px; /* Slightly wider for better overlay feel */
	height: 100%;
	background-color: var(--accent-delete);
	display: flex;
	align-items: center;
	justify-content: center;
	border-radius: 0 32rpx 32rpx 0;
	z-index: 2; /* Slide in on top */
	transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}
.delete-text {
	color: #FFFFFF;
	font-size: 28rpx;
	font-weight: 600;
}

/* Custom CSS Trash Icon - Refined: Bolder lines, shorter body */
.custom-trash {
	width: 32rpx;
	height: 34rpx; /* Reduced total height */
	display: flex;
	flex-direction: column;
	align-items: center;
	margin-right: 16rpx;
}
.trash-lid {
	width: 100%;
	height: 5rpx; /* Slightly bolder lid */
	background-color: #FFFFFF;
	position: relative;
	border-radius: 3rpx;
	margin-bottom: 4rpx;
}
.trash-lid::before {
	content: '';
	position: absolute;
	top: -5rpx; /* Adjusted for bolder handle */
	left: 50%;
	transform: translateX(-50%);
	width: 14rpx; /* Wider handle */
	height: 5rpx;
	background-color: #FFFFFF;
	border-radius: 3rpx 3rpx 0 0;
}
.trash-body {
	flex: 1;
	width: 28rpx; /* Slightly wider body */
	border: 4rpx solid #FFFFFF; /* Bolder border */
	border-top: none;
	border-radius: 0 0 8rpx 8rpx;
	display: flex;
	justify-content: space-evenly;
	padding-top: 5rpx;
}
.trash-line {
	width: 4rpx; /* Bolder internal lines */
	height: 12rpx; /* Shorter lines to fit shorter body */
	background-color: #FFFFFF;
	border-radius: 3rpx;
}

/* Footer Input */
.footer-input-container {
	position: fixed;
	bottom: 0;
	left: 0;
	width: 100%;
	padding: 40rpx;
	z-index: 100;
}

.glass-panel {
	background: rgba(255, 255, 255, 0.7);
	backdrop-filter: blur(12px);
	border: 1px solid rgba(255, 255, 255, 0.5);
	border-radius: 60rpx; /* Increased for rounder look */
	padding: 16rpx;
	display: flex;
	align-items: center;
	box-shadow: 0 20rpx 50rpx rgba(31, 38, 135, 0.1);
}

.input-wrapper {
	flex: 1;
	display: flex;
	align-items: center;
	padding-left: 30rpx;
	height: 96rpx;
}

.add-plus-icon {
	font-size: 40rpx;
	color: #E2E8F0; /* Much lighter color */
	margin-right: 20rpx;
	line-height: 1;
}

.main-input {
	flex: 1;
	height: 96rpx;
	font-size: 32rpx;
	color: var(--text-main);
	line-height: 96rpx;
}

.placeholder {
	color: #E2E8F0; /* Lighter placeholder color */
}

.send-btn {
	width: 96rpx;
	height: 96rpx;
	border-radius: 40rpx; /* Increased for rounder look */
	display: flex;
	align-items: center;
	justify-content: center;
	transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.send-btn-disabled {
	background-color: #F1F5F9;
	box-shadow: none;
}
.send-btn-disabled .arrow-icon {
	color: #CBD5E1;
}

.send-btn-active {
	background-color: var(--primary);
	box-shadow: 0 8rpx 20rpx rgba(59, 130, 246, 0.4);
}
.send-btn-active .arrow-icon {
	color: #FFFFFF;
}

.arrow-icon {
	font-size: 40rpx;
	font-weight: bold;
}

/* Dark Mode Overrides */
@media (prefers-color-scheme: dark) {
	.glass-panel {
		background: rgba(30, 41, 59, 0.7);
		border: 1px solid rgba(255, 255, 255, 0.1);
	}
	.send-btn-disabled {
		background-color: #334155;
	}
	.send-btn-disabled .arrow-icon {
		color: #475569;
	}
	.checkbox {
		border-color: #334155;
	}
	.tab-btn {
		background-color: #1E293B;
		box-shadow: none;
	}
	.task-card {
		box-shadow: none;
		background-color: #1E293B;
	}
	.task-done {
		background-color: rgba(59, 130, 246, 0.1);
	}
	.blob-1 { opacity: 0.2; }
	.blob-2 { opacity: 0.2; }
}
</style>

