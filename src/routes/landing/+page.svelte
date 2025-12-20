<script lang="ts">
	import { goto } from '$app/navigation';
	import { ArrowRight } from '@lucide/svelte';
	import { isAuthenticated, authLoading } from '$lib/stores/auth';
	import { onMount } from 'svelte';

	function handleDashboardClick() {
		if ($isAuthenticated) {
			goto('/');
		} else {
			goto('/register');
		}
	}
</script>

<div class="flex h-screen flex-col overflow-hidden bg-[#f7f4ec] text-slate-900">
	<!-- Navigation (Medium-like) -->
	<nav class="shrink-0 border-b border-black/15">
		<div class="mx-auto max-w-6xl px-6 py-3">
			<div class="flex items-center justify-between gap-6">
				<a href="/landing" class="text-2xl font-semibold tracking-tight">Lector</a>

				<div class="flex items-center gap-3 sm:gap-4">
					<div class="hidden items-center gap-5 text-sm text-slate-900/90 md:flex">
						<a class="underline-offset-4 hover:underline" href="/landing">the story</a>
						<a class="underline-offset-4 hover:underline" href="/landing">How it works</a>
						<a class="underline-offset-4 hover:underline" href="/landing">Support</a>
					</div>

					<button
						type="button"
						on:click={handleDashboardClick}
						class="inline-flex items-center justify-center rounded-full bg-black px-4 py-2 text-sm font-medium text-white transition hover:bg-black/85"
					>
						Get started
					</button>
				</div>
			</div>
		</div>
	</nav>

	<!-- Hero -->
	<section class="flex flex-1 items-center overflow-hidden border-b border-black/15">
		<div class="mx-auto grid w-full max-w-6xl items-center gap-8 px-6 lg:grid-cols-2">
			<div class="max-w-xl">
				<p class="mb-3 text-xs font-semibold tracking-[0.18em] text-slate-900/70 uppercase">
					Lector • focused reading
				</p>

				<h1
					class="text-[2.8rem] leading-[0.95] tracking-tight text-slate-950 sm:text-[3.8rem] lg:text-[6rem]"
					style="font-family: ui-serif, Georgia, Cambria, 'Times New Roman', Times, serif;"
				>
					Where reading feels right
				</h1>

				<p class="mt-5 max-w-prose text-sm leading-relaxed text-slate-800/80 sm:text-lg">
					Turn long texts into something you actually want to sit with.
				</p>

				<div class="mt-6 flex flex-col gap-3 sm:flex-row sm:items-center">
					<a
						href="/login"
						class="inline-flex w-full items-center justify-center gap-2 rounded-full bg-black px-6 py-2.5 text-sm font-medium text-white transition hover:bg-black/85 sm:w-auto"
					>
						Start reading
						<ArrowRight class="h-4 w-4" />
					</a>
				</div>

				{#if $authLoading}
					<p class="mt-3 text-xs text-slate-700/70">Loading session…</p>
				{/if}
			</div>

			<!-- Visual placeholder (no images) -->
			<div class="hidden lg:block">
				<div
					class="relative aspect-4/3 w-full overflow-hidden rounded-[28px] border border-black/15 bg-[#f7f4ec]"
				>
					<div
						class="absolute inset-0"
						style="background:
							radial-gradient(600px 280px at 20% 20%, rgba(0,0,0,0.08), transparent 60%),
							radial-gradient(520px 260px at 80% 30%, rgba(0,0,0,0.06), transparent 62%),
							radial-gradient(700px 380px at 50% 85%, rgba(0,0,0,0.05), transparent 60%);"
					></div>
					<div
						class="absolute right-5 bottom-5 left-5 rounded-2xl border border-black/10 bg-white/40 p-4"
					>
						<p class="text-sm font-medium text-slate-900/85">Your library, your way</p>
						<p class="mt-1 text-xs text-slate-900/60">Theme, size, spacing, and focus.</p>
					</div>
				</div>
			</div>
		</div>
	</section>

	<!-- Footer (simple, Medium-ish) -->
	<footer class="shrink-0 border-t border-black/10">
		<div class="mx-auto max-w-6xl px-6 py-3">
			<div
				class="flex flex-col items-center justify-between gap-2 text-xs text-slate-900/60 sm:flex-row"
			>
				<p>© {new Date().getFullYear()} Lector</p>
				<div class="flex flex-wrap items-center justify-center gap-x-4 gap-y-1">
					<a class="underline-offset-4 hover:text-slate-900 hover:underline" href="/landing">Help</a
					>
					<a class="underline-offset-4 hover:text-slate-900 hover:underline" href="/landing"
						>Status</a
					>
					<a class="underline-offset-4 hover:text-slate-900 hover:underline" href="/landing"
						>About</a
					>
					<a class="underline-offset-4 hover:text-slate-900 hover:underline" href="/landing"
						>Privacy</a
					>
					<a class="underline-offset-4 hover:text-slate-900 hover:underline" href="/landing"
						>Terms</a
					>
					<a
						class="underline-offset-4 hover:text-slate-900 hover:underline"
						href="mailto:marlon.castro@thefndrs.com"
					>
						Contact
					</a>
				</div>
			</div>
		</div>
	</footer>
</div>
