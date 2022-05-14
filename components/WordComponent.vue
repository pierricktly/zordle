<template>
	<div class="space-y-2.5 p-2 bg-white rounded">
		<div v-for="(row, index) in wordToFind" :key="`row_${index}`"  class="flex gap-2.5">
			<letter-component 
				v-for="(letter, index) in row" 
				:key="`letter_${letter}_${index}`"
				:letter="letter.letter"
				:statut="letter.statut"
			/>
		</div>
	</div> 
</template>

<script>
export default {
	name: 'WordComponent',

	props: {
		wordToFind: {
			type: Array,
			required: true
		},
	},
}
</script>