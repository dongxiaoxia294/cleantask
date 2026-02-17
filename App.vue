<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
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
	}

	/* Color Variables for easier maintenance */
	:root {
		--primary: #3B82F6;
		--primary-soft: #DBEAFE;
		--background: #F8FAFC;
		--card: #FFFFFF;
		--text-main: #1E293B;
		--text-sub: #64748B;
		--accent-delete: #FF6B6B;
	}

	/* Dark Mode support */
	@media (prefers-color-scheme: dark) {
		page {
			background-color: #0F172A;
			color: #F1F5F9;
		}
		:root {
			--background: #0F172A;
			--card: #1E293B;
			--text-main: #F1F5F9;
			--text-sub: #94A3B8;
		}
	}

	view, text, scroll-view, input, button {
		box-sizing: border-box;
	}
</style>

