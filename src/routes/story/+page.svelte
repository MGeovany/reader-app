<script lang="ts">
	import { ArrowLeft } from '@lucide/svelte';
	import { goto } from '$app/navigation';
</script>

<div class="bg-cream min-h-screen">
	<!-- Navigation -->
	<nav class="border-b border-black/15">
		<div class="mx-auto max-w-4xl px-6 py-4">
			<button
				type="button"
				on:click={() => goto('/landing')}
				class="inline-flex items-center gap-2 text-sm text-slate-900/70 transition hover:text-slate-900"
			>
				<ArrowLeft class="h-4 w-4" strokeWidth={1.5} />
				Back
			</button>
		</div>
	</nav>

	<!-- Content -->
	<main class="mx-auto max-w-3xl px-6 py-16">
		<h1 class="mb-4 text-5xl font-light tracking-tight text-slate-950 sm:text-6xl">The Story</h1>
		<p class="mb-12 text-sm text-slate-600">Why Lector exists</p>

		<div class="prose max-w-none prose-slate">
			<p class="text-lg leading-relaxed text-slate-800">
				Getting books here in Honduras is hard enough, but finding good PDFs in proper format or
				well-formatted ones is a whole other thing. Sometimes you have to download Google extensions
				just to get dark mode so you can read more comfortably, extensions that usually stop working
				after a week or ask you to pay for a subscription.
			</p>

			<p class="mt-6 leading-relaxed text-slate-800">
				Adobe Reader and other applications are made for different things. They're built for
				contracts and documents.
			</p>

			<p class="mt-6 leading-relaxed text-slate-800">
				I'm of the mindset that it's better to have one tool that solves a single problem really
				well, that's why Lector was created.
			</p>

			<p class="mt-6 leading-relaxed text-slate-800">
				Every little detail is designed to help you concentrate. You can change the fonts, choose
				themes that feel right, and the reader remembers exactly where you left off.
			</p>

			<p class="mt-6 leading-relaxed text-slate-800">
				Down the line, I'd like Lector to be more than just a personal reading space. I'd love for
				it to become a place where people share books they've found, recommend good reads, discover
				new authors, and talk about them.
			</p>

			<p class="mt-10 text-lg leading-relaxed text-slate-800">
				For now, Lector is simply a quiet place to read and concentrate.
			</p>
		</div>

		<div class="mt-16 border-t border-black/10 pt-12">
			<p class="text-sm text-slate-600">
				Want to learn more? <a href="/about" class="underline-offset-4 hover:underline"
					>Read about Lector</a
				>
				or{' '}
				<a href="/contact" class="underline-offset-4 hover:underline">get in touch</a>.
			</p>
		</div>
	</main>
</div>
