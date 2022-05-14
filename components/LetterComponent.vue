<template>
	<div 
	class="flex flex-col items-center justify-center aspect-square rounded w-20 h-20"
	:class="{
		'bg-green-500': statut == 'ACCEPTED', 
		'bg-red-500': statut == 'REJECTED', 
		'bg-yellow-500': statut == 'FOUND', 
		'bg-cyan-400': statut == 'NEUTRAL'
	}">
		<p class="text-white font-bold text-5xl uppercase">{{ letter }}</p>
	</div>
</template>

<script>
	export default {
		name: 'LetterComponent',
		props: {
			letter: {
				type: String,
				default: '.'
			},
			statut: {
				type: String,
				default: 'NEUTRAL'
			}
		}
	}
</script>