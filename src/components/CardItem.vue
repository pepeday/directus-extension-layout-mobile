<template>
	<div class="content">
		<!-- Title with complex rendering setup -->
		<render-template v-if="title" class="title" :collection="collection" :item="item" :template="title" />
		<!-- Subtitle with complex rendering setup -->
		<render-template v-if="isSubtitleExpanded && subtitle" class="subtitle" :collection="collection" :item="item"
			:template="subtitle" />

		<!-- Tags -->
		<render-template :collection="collection" :item="item" :template="tag" />

	</div>
</template>




<script lang="ts">
import { useI18n } from 'vue-i18n';
import { defineComponent, PropType } from 'vue';
import { LayoutOptions } from '../types'; // Ensure this path matches your project structure

//import { useSync } from '@directus/extensions-sdk';

export default defineComponent({
	props: {
		isSubtitleExpanded: {
			type: Boolean,
			default: false

		},
		layoutOptions: {
			type: Object as PropType<LayoutOptions>,
			required: false,
		},
		collection: {
			default: null,
		},
		title: {
			type: String,
			default: "Title",
		},
		subtitle: {
			type: String,
			default: "Sub title",
		},
		tag: {
			type: String,
			default: null,
		},
		status: {
			type: String,
			default: null,
		},
		id: {
			default: null,
		},
		selectMode: {
			type: String,
			default: false,
		},
		statusClass: {
			type: String,
			default: 'color_primary',
		},
		item: {
			type: Object,
			default: null,
		},
		idShow: {
			type: Boolean
		}
	},
	setup(props) {
		const { t } = useI18n();
		return { t };
	},
});
</script>

<style lang="scss" scoped>
.content {
	width: 100%;
	display: flex;
	flex-direction: column;
	align-items: flex-start;
	overflow: visible;
	justify-content: flex-start;
	/* Allow content within to overflow if needed */
}

.content .title {
	font-size: 1.0rem;
	font-weight: bold;
	color: var(--text-primary);
	margin-bottom: 4px;

}

.content .title {
	display: -webkit-box;
	display: box;
	/* Future compatibility */
	-webkit-line-clamp: 3;
	/* Limit to 3 lines */
	line-clamp: 3;
	/* Future compatibility */
	-webkit-box-orient: vertical;
	box-orient: vertical;
	/* Future compatibility */
	overflow: hidden;
	text-overflow: ellipsis;
	max-height: calc(1.2em * 3);
	/* Adjust to match line height */
	transition: max-height 0.3s ease, -webkit-line-clamp 0.3s ease;
	/* Add transition for smooth expansion */
}

.content .subtitle {
	display: -webkit-box;
	-webkit-box-orient: vertical;
	-webkit-line-clamp: 12; /* Limit to 3 lines */
	line-clamp: 12;
	overflow: hidden;
	text-overflow: ellipsis;
	max-height: calc(1.2em * 12); /* Adjust to match line height */
	transition: max-height 0.3s ease, -webkit-line-clamp 0.3s ease; /* Smooth expansion transition */
	font-size: 1.0rem;
	color: var(--text-muted);
	font-weight: normal;
	margin-bottom: 8px;
}

</style>
