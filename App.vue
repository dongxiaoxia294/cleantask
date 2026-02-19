<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
			// Listen to theme change
			if (typeof uni.onThemeChange === 'function') {
				uni.onThemeChange((res) => {
					console.log('Theme changed to:', res.theme);
					this.updateSystemUI(res.theme);
				});
			}
		},
		onShow: function() {
			console.log('App Show')
			const res = uni.getSystemInfoSync();
			const theme = res.osTheme || res.theme || 'light';
			console.log('Current theme:', theme);
			this.updateSystemUI(theme);
		},
		onHide: function() {
			console.log('App Hide')
		},
		methods: {
			updateSystemUI(theme) {
				const isDark = theme === 'dark';
				const style = isDark ? 'light' : 'dark';
				
				// Status Bar (Top)
				if (typeof uni.setStatusBarStyle === 'function') {
					uni.setStatusBarStyle({ style });
				} else if (typeof plus !== 'undefined' && plus.navigator) {
					plus.navigator.setStatusBarStyle(style);
				}
				
				// Navigation Bar 颜色同步在 index.vue 中处理
			}
		}
	}
</script>

<style>
	/* Global Styles based on Modern Task Dashboard V1 */
	page {
		height: 100%;
		/* System default fonts as requested, but with specific weights */
		font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
		background-color: #F8FAFC;
		color: #1E293B;
		/* 延伸到安全区域外 */
		padding-bottom: constant(safe-area-inset-bottom);
		padding-bottom: env(safe-area-inset-bottom);
	}

	/* Color Variables for easier maintenance */
	page {
		--primary: #3B82F6;
		--primary-soft: #DBEAFE;
		--background: #F8FAFC;
		--background-gradient-start: #F8FAFC;
		--background-gradient-end: #EFF6FF;
		--card: #FFFFFF;
		--text-main: #1E293B;
		--text-sub: #64748B;
		--accent-delete: #FF6B6B;
		--glass-bg: rgba(255, 255, 255, 0.7);
		--glass-border: rgba(255, 255, 255, 0.5);
		--blob-opacity: 0.15;
		--blob-1-color: #3B82F6;
		--blob-2-color: #A855F7;
		--blob-3-color: #3B82F6;
		--system-nav-bg: #F8FAFC;
	}

	/* Dark Mode support */
	@media (prefers-color-scheme: dark) {
		page {
			background-color: #000000;
			color: #F8FAFC;
			--background: #000000;
			--background-gradient-start: #000000;
			--background-gradient-end: #000000;
			--card: #1C1C1E;
			--primary-soft: #2C2C2E;
			--text-main: #F8FAFC;
			--text-sub: #8E8E93;
			--glass-bg: rgba(28, 28, 30, 0.8);
			--glass-border: rgba(255, 255, 255, 0.05);
			--blob-opacity: 0.05;
			--blob-1-color: #3B82F6;
			--blob-2-color: #A855F7;
			--blob-3-color: #3B82F6;
			--system-nav-bg: #000000;
		}
	}

	/* Manual Dark Mode selector for environments where media queries are inconsistent */
	.dark {
		background-color: #000000;
		color: #F8FAFC;
		--background: #000000;
		--background-gradient-start: #000000;
		--background-gradient-end: #000000;
		--card: #1C1C1E;
		--primary-soft: #2C2C2E;
		--text-main: #F8FAFC;
		--text-sub: #8E8E93;
		--glass-bg: rgba(28, 28, 30, 0.8);
		--glass-border: rgba(255, 255, 255, 0.05);
		--blob-opacity: 0.05;
		--blob-1-color: #3B82F6;
		--blob-2-color: #A855F7;
		--blob-3-color: #3B82F6;
		--system-nav-bg: #000000;
	}

	view, text, scroll-view, input, button {
		box-sizing: border-box;
	}
</style>

