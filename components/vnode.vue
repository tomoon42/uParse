<template>
	<view :style="node.tagType === 'inline' || node.node == 'text' ? 'display:inline-block;' : ''">
		<!-- 有子节点 -->
		<block v-if="node.node == 'element'">
			<block v-if="node.tag == 'button'">
				<button type="default" size="mini">
					<block v-for="(node, index) of node.nodes"  :key="index">
						<vnode :node="node"></vnode>
					</block>
				</button>
			</block>
			
			<!--li类型-->
			<block v-else-if="node.tag == 'li'">
				<view :class="node.classStr" :style="node.styleStr">
					<block v-for="(node, index) of node.nodes"  :key="index">
						<vnode :node="node"></vnode>
					</block>
				</view>
			</block>
			
			<!--video类型-->
			<block v-else-if="node.tag == 'video'">
				<wx-parse-video :node="node" />
			</block>
			
			<!--audio类型-->
			<block v-else-if="node.tag == 'audio'">
				<wx-parse-audio :node="node" />
			</block>
			
			<!--img类型-->
			<block v-else-if="node.tag == 'img'">
				<wx-parse-img :node="node" />
			</block>
			
			<!--a类型-->
			<block v-else-if="node.tag == 'a'">
				<view @click="wxParseATap" :class="node.classStr" :data-href="node.attr.href" :style="node.styleStr">
					<block v-for="(node, index) of node.nodes" :key="index">
						<vnode :node="node"></vnode>
					</block>
				</view>
			</block>
			
			<!--br类型-->
			<block v-else-if="node.tag == 'br'">
				<text>\n</text>
			</block>
			
			<!--其他标签-->
			<block v-else>
				<view :class="node.classStr" :style="node.styleStr">
					<block v-for="(node, index) of node.nodes" :key="index">
						<vnode :node="node"></vnode>
					</block>
				</view>
			</block>
		</block>
		<!-- 无子节点 -->
		<block v-else-if="node.node == 'text'">
			<text>{{ node.text }}</text>
		</block>
		<block v-else><slot></slot></block>
	</view>
</template>

<script>
import wxParseImg from './wxParseImg';
import wxParseVideo from './wxParseVideo';
import wxParseAudio from './wxParseAudio';
export default {
	name: 'Vnode',
	props: {
		node: {},
	},
	components: {
		wxParseImg,
		wxParseVideo,
		wxParseAudio,
	},
	methods: {
		wxParseATap(e) {
			const {
				href
			} = e.currentTarget.dataset;
			if (!href) return;
			let parent = this.$parent;
			while(!parent.preview || typeof parent.preview !== 'function') {
				parent = parent.$parent;
			}
			parent.navigate(href, e);
		},
	},
}
</script>

<style>
.inline {
	display: inline-block !important;
}

.block {
	width: 100% !important;
}

/* 对标签有其它特殊需求可以在这里自定义 */
</style>