<template>
	<v-sheet class="card" @click="handleClick" :class="{
		loading,
		readonly,
		selected: item && modelValue.includes(item[itemKey]),
		'select-mode': selectMode,
	}">
		<!-- Round button for selection -->
		<v-button icon rounded @click.stop="toggleSelection" :secondary="!isSelected"
			:class="{ 'selected-button': isSelected, 'unselected-button': !isSelected }" class="select-button">
			<v-icon name="check_circle" />
		</v-button>

		<!-- Card content -->
		<div class="card-content-wrapper" :class="{ expanded: isSubtitleExpanded }">
			<div :class="`card-${size}`">
				<card-item :id="item.id" :item="item" :collection="collection"
					:image="imageSource && imageSource?.id || null" :selectMode="selectionIcon" :title="title"
					:subtitle="subtitle" :tag="tag" :idShow="idShow" :statusClass="statusClass(item.status)"
					:isSubtitleExpanded="isSubtitleExpanded" :classImgFit="imgFit()" />
			</div>
			<!-- Expand / Collapse Button-->
			<v-button icon rounded @click.stop="toggleSubtitle" :small="true" :secondary="true"
				class="toggle-subtitle-button">
				<v-icon :name="isSubtitleExpanded ? 'expand_less' : 'expand_more'" />
			</v-button>
		</div>

	</v-sheet>
</template>




<script lang="ts">
import {
	computed,
	defineComponent,
	PropType,
	ref,
} from "vue";
import { useI18n } from "vue-i18n";
import { useRouter } from "vue-router";
import CardItem from "./CardItem.vue";
type File = {
	[key: string]: any;
	id: string;
	type: string;
	modified_on: Date;
};
export default defineComponent({
	components: { CardItem },
	props: {
		collection: {
			type: String, // Changed from `default: null` or similar to `type: String`
			default: null,
		},
		icon: {
			type: String,
			default: "box",
		},
		file: {
			type: Object as PropType<File>,
			default: null,
		},
		crop: {
			type: Boolean,
			default: false,
		},
		loading: {
			type: Boolean,
			default: false,
		},
		item: {
			type: Object as PropType<
				Record<string, any>
			>,
			default: null,
		},
		modelValue: {
			type: Array as PropType<
				(string | number)[]
			>,
			default: () => [],
		},
		selectMode: {
			type: Boolean,
			default: false,
		},
		to: {
			type: String,
			default: "",
		},
		readonly: {
			type: Boolean,
			default: false,
		},
		itemKey: {
			type: String,
			required: true,
		},
		imageSource: {
			type: String,
		},
		title: {
			type: String,
			default: "title",
		},
		subtitle: {
			type: String,
			default: "subtitle",
		},
		imageFit: {
			type: String,
		},
		tag: {
			type: String,
			default: false,
		},
		size: {
			type: Number,
			default: 1,
		},
		idShow: {
			type: Boolean,
		},
	},
	emits: ["update:modelValue"],
	setup(props, { emit }) {
		const router = useRouter();
		const imgError = ref(false);
		const { t } = useI18n();
		const isSubtitleExpanded = ref(false);

		function toggleSubtitle() {
			isSubtitleExpanded.value = !isSubtitleExpanded.value;
		}


		// Define isSelected as a computed property
		const isSelected = computed(() =>
			props.modelValue.includes(props.item?.[props.itemKey])
		);


		const selectionIcon = computed(() => {
			if (!props.item) return "radio_button_unchecked";
			return isSelected.value ? "check_circle" : "radio_button_unchecked";
		});


		function toggleSelection():
			| void
			| any {
			if (!props.item)
				return null;

			if (
				props.modelValue.includes(
					props.item[
					props.itemKey
					]
				)
			) {
				emit(
					"update:modelValue",
					props.modelValue.filter(
						(key) =>
							key !==
							props.item[
							props
								.itemKey
							]
					)
				);
			} else {
				emit(
					"update:modelValue",
					[
						...props.modelValue,
						props.item[
						props
							.itemKey
						],
					]
				);
			}
		}

		function handleClick(): void {
			if (
				props.selectMode ===
				true
			) {
				toggleSelection();
			} else {
				router.push(props.to);
			}
		}

		function imgFit(): string {
			return props.imageFit ==
				"crop"
				? "object-cover"
				: "object-contain";
		}

		function statusClass(
			status: string
		): string {
			return status ===
				"published"
				? "color_primary"
				: status === "archived"
					? "color_archire"
					: "color_sub";
		}

		return {
			t,
			selectionIcon,
			toggleSelection,
			handleClick,
			imgError,
			imgFit,
			statusClass,
			isSelected,
			isSubtitleExpanded,
			toggleSubtitle
		};
	},
});
</script>

<style lang="scss" scoped>
.loading {
	.header {
		margin-bottom: 8px;
	}
}

.select-button {
	flex-shrink: 0;
	/* Keeps button size fixed */
}


.card_img {
	width: 150px;
	object-fit: cover;
	height: 150px;
}

.select-button {
	flex-shrink: 0;
	/* Prevents the button from resizing */
	//z-index: 10;
	/* Ensures button is above the content */
}



.card {
	position: relative;
	cursor: pointer;
	display: flex;
	align-items: flex-start;
	padding: 10px;
	gap: 8px;
	overflow: visible;

	&:hover {
		background-color: var(--theme--background-accent); // Lighten background on hover
	}

	.card.expanded {
		background-color: var(--theme--background-accent);
		transition: background-color 0.3s ease;
	}

	.card-content-wrapper {
		display: flex;
		flex-direction: row;
		/* Align items horizontally */
		align-items: flex-start;
		/* Aligns content to the top */
		gap: 8px;
		/* Adds space between elements */
		width: 100%;
		/* Ensures the wrapper takes the full width */
		transition: max-height 0.3s ease;

		&.expanded {
			max-height: 500px;
			/* Adjust based on your needs */
		}
	}

	.toggle-subtitle-button {
		align-self: flex-start;
		/* Aligns the button to the top */
		margin-left: auto;
		/* Pushes the button to the right */
	}

	.conten
	/* Style for ensuring .content layout with tags */

	.header {
		position: relative;
		z-index: 1;
		display: flex;
		align-items: center;
		justify-content: center;
		width: 100%;
		overflow: hidden;
		background-color: var(--theme--background-normal);
		border-color: var(--primary-50);
		border-style: solid;
		border-width: 0px;
		border-radius: var(--border-radius);
		transition: border-width var(--fast) var(--transition);

		&::after {
			display: block;
			padding-bottom: 100%;
			content: "";
		}

		.image {
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 100%;
			object-fit: contain;
		}

		.svg {
			position: absolute;
			width: 75%;
			height: 75%;
			object-fit: contain;
		}

		.type {
			color: var(--foreground-subdued);
			text-transform: uppercase;
		}

		.v-icon {
			--v-icon-color: var(--foreground-subdued);
		}

		.v-skeleton-loader {
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 100%;
		}

		.selection-fade {
			position: absolute;
			top: 0;
			left: 0;
			z-index: 1;
			width: 100%;
			height: 48px;
			opacity: 0;
			transition: opacity var(--fast) var(--transition);

			&::before {
				position: absolute;
				top: 0;
				left: 0;
				width: 100%;
				height: 100%;
				background-image: linear-gradient(-180deg,
						rgb(38 50 56 / 0.1) 10%,
						rgb(38 50 56 / 0));
				content: "";
			}
		}
	}

	&::before {
		position: absolute;
		top: 7px;
		left: 7px;
		z-index: 2;
		width: 18px;
		height: 18px;
		background-color: var(--background-page);
		border-radius: 24px;
		opacity: 0;
		transition: opacity var(--fast) var(--transition);
		content: "";
	}

	.selector {
		color: var(--v-icon-color);
		position: absolute;
		top: 0px;
		left: 0px;
		z-index: 11;
		margin: 4px;
		opacity: 0;
		transition: opacity var(--fast) var(--transition),
			color var(--fast) var(--transition);

		&:hover {
			opacity: 1 !important;
		}
	}

	&.select-mode {
		.selector {
			opacity: 0.5;
		}

		.header {
			.selection-fade {
				opacity: 1;
			}
		}
	}

	&.selected {
		&::before {
			opacity: 1;
		}

		.selector {
			--v-icon-color: var(--primary);
			--v-icon-color-hover: var(--primary);

			opacity: 1;
		}

		.header {
			border-width: 12px;

			.selection-fade {
				opacity: 1;
			}
		}
	}

	&:hover {
		.selector {
			opacity: 2;
		}

		.header {
			.selection-fade {
				opacity: 1;
			}
		}
	}
}


.readonly {
	pointer-events: none;
}
</style>
