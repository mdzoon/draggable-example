<template>
	<div class="fluid container">
	  	<div class="form-group form-group-lg panel panel-default">
			<div class="panel-heading">
				<h3 class="panel-title">Sortable control</h3>
			</div>
			<div class="panel-body">
				<div class="checkbox">
					<label><input type="checkbox" v-model="editable">Enable drag and drop</label>
				</div>
				<button type="button" class="btn btn-default" @click="orderPositions">Sort by original order</button>
			</div>
	  	</div>
  
	  	<div class="col-md-9">
			<draggable
				class="list-group"
				tag="ul"
				v-model="positions"
				v-bind="draggableOptions"
				@start="isDragging = true"
				@end="isDragging = false"
			>
				<transition-group type="transition" :name="'flip-list'">
					<NestedPosition
						v-for="position in positions.filter(p => p.parent_id === null)"
						:key="position.id"
						:position="position"
						:positions="positions"
						:draggableOptions="draggableOptions"
						@update-positions="updatePositions"
					/>
				</transition-group>
			</draggable>
	  	</div>
  
		<div class="list-group col-md-3">
			<pre>{{ positionsString }}</pre>
		</div>
	</div>
</template>
  
<script>
import draggable from "vuedraggable"
import NestedPosition from "./NestedPosition.vue"
  
export default {
	name: "DraggableList",
	components: {
		draggable,
		NestedPosition,
	},
	data() {
	  	return {
			positions: [
				{ id: 10, title: "Test 10", has_children: false, parent_id: 3, index: '3b' },
				{ id: 12, title: "Test 12", has_children: false, parent_id: 3, index: '3d' },
				{ id: 9, title: "Test 9", has_children: false, parent_id: 3, index: '3a' },
				{ id: 11, title: "Test 11", has_children: false, parent_id: 3, index: '3c' },
				{ id: 5, title: "Test 5", has_children: false, parent_id: 2, index: '2a' },
				{ id: 6, title: "Test 6", has_children: false, parent_id: 2, index: '2b' },
				{ id: 7, title: "Test 7", has_children: false, parent_id: 2, index: '2c' },
				{ id: 8, title: "Test 8", has_children: true, parent_id: 2, index: '2d' },
				{ id: 1, title: "Test 1", has_children: false, parent_id: null, index: '3' },
				{ id: 2, title: "Test 2", has_children: true, parent_id: null, index: '2' },
				{ id: 3, title: "Test 3", has_children: true, parent_id: null, index: '4' },
				{ id: 13, title: "Test 13", has_children: true, parent_id: 8, index: '2da' },
				{ id: 14, title: "Test 14", has_children: false, parent_id: 13, index: '2daa' },
				{ id: 15, title: "Test 15", has_children: false, parent_id: 13, index: '2dab' },
				{ id: 4, title: "Test 4", has_children: false, parent_id: null, index: '1' },
				{ id: 16, title: "Test 16", has_children: false, parent_id: 15, index: '2daba' },
				{ id: 17, title: "Test 17", has_children: false, parent_id: 16, index: '2dabaa' },
				{ id: 18, title: "Test 18", has_children: false, parent_id: 17, index: '2dabaaa' },
			],
			originalOrder: [],
			editable: true,
			isDragging: false,
			delayedDragging: false,
	  	}
	},
	created() {
		this.sortInitialPositions()
		this.assignOrder()
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
	  	draggableOptions() {
			return {
				animation: 0,
				disabled: !this.editable,
				ghostClass: "ghost",
			}
	  	},
	  	positionsString() {
			return JSON.stringify(this.positions, null, 2)
	  	},
	},	
	methods: {
		sortInitialPositions() {
			let sortedPositions = []
			const addChildren = (parentId) => {
				const children = this.positions.filter(p => p.parent_id === parentId)
				children.sort((a, b) => a.index.localeCompare(b.index))

				children.forEach(child => {
					sortedPositions.push(child)
					addChildren(child.id)
				})
			}
			const topLevelPositions = this.positions.filter(position => position.parent_id === null)
			topLevelPositions.sort((a, b) => a.index.localeCompare(b.index))
			topLevelPositions.forEach(parent => {
				sortedPositions.push(parent)
			})

			topLevelPositions.forEach(parent => {
				addChildren(parent.id)
			})

			this.positions = sortedPositions
		},
		assignOrder() {
			this.positions.forEach((position, index) => {
				position.originalOrder = index
			})
		},		
		orderPositions() {
			this.positions.sort((one, two) => one.originalOrder - two.originalOrder)
		},
		updatePositions(newPositions) {
        	this.positions = newPositions
    	}		
	}
}
</script>

<style>
.sortable-chosen {
	border: 1px solid rgba(52, 195, 143, 0.5) !important;
	background-color: rgba(52, 195, 143, 0.05) !important;
}
.sortable-chosen * {
	border: 1px solid rgba(52, 195, 143, 0.5) !important;
	background-color: rgba(52, 195, 143, 0.05) !important;
}
.ghost {
  	opacity: 0.5;
}
.list-group {
  	min-height: 20px;
}
.list-group-item {
  	cursor: move;
}
.list-group-item i {
  	cursor: pointer;
}
</style>
