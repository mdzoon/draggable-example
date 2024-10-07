<template>
    <li class="list-group-item">
		<i
			class="glyphicon glyphicon-pushpin"
			aria-hidden="true"
		></i>
      	{{ position.title }}
      	<span class="badge">{{ position.originalOrder + 1 }}</span>
  
      	<ul v-if="hasChildren">
			<draggable
				tag="ul"
				:list="children"
				v-bind="draggableOptions"
				@start="handleDragStart"
				@end="handleDragEnd"
			>
				<NestedPosition
					v-for="child in children"
					:key="child.id"
					:position="child"
					:positions="positions"
					:draggableOptions="draggableOptions"
				/>
        	</draggable>
      	</ul>
    </li>
</template>
  
<script>
import draggable from "vuedraggable"
  
export default {
    name: "NestedPosition",
    components: {
    	draggable,
    },
    props: {
		position: {
			type: Object,
			required: true,
		},
		positions: {
			type: Array,
			required: true,
		},
		draggableOptions: {
			type: Object,
			required: true,
		},
    },
    data() {
		return {
			isDragging: false,
			delayedDragging: false,
			tempChildren: [],
		}
    },
	watch: {
		isDragging(newValue) {
			if (newValue) {
				this.delayedDragging = true
				return
			}
			this.$nextTick(() => {
		  		this.delayedDragging = false
			})
		}
	},	
    computed: {
		hasChildren() {
			return this.children.length > 0
		},
		children() {
			return this.positions.filter(p => p.parent_id === this.position.id)
		},
    },
    methods: {
		handleDragStart() {
			this.isDragging = true
			this.tempChildren = [...this.children]
		},
		handleDragEnd(event) {
			const reorderedChildren = [...this.children]
			this.tempChildren.forEach((tempChild, index) => {
				const newIndex = reorderedChildren.findIndex(child => child.id === tempChild.id)
				if (newIndex !== index) {
					const originalIndex = this.positions.findIndex(pos => pos.id === tempChild.id)
					const moveDelta = newIndex - index

					if (originalIndex !== -1) {
						const [movedPosition] = this.positions.splice(originalIndex, 1)
						this.positions.splice(originalIndex + moveDelta, 0, movedPosition)
					}
				}
			})

        	this.$emit('update-positions', this.positions)
			this.isDragging = false
    	},
	}
}
</script>
  