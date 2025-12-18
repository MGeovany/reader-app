<script lang="ts">
	import ProtectedRoute from '$lib/components/ProtectedRoute.svelte';
	import type { Document, TextBlock } from '$lib/api/types';
	import { goto } from '$app/navigation';
	import { ArrowLeft, ChevronLeft, ChevronRight, Moon, Sun, Type } from '@lucide/svelte';
	import TextPreference from '$lib/components/TextPreference.svelte';

	export let data: { document: Document };

	const fontOptions = [
		{
			key: 'roboto',
			label: 'Roboto',
			stack: '"Roboto", system-ui, sans-serif'
		},
		{
			key: 'merriweather',
			label: 'Merriweather',
			stack: '"Merriweather", Georgia, serif'
		},
		{
			key: 'georgia',
			label: 'Georgia',
			stack: 'Georgia, "Times New Roman", serif'
		},
		{
			key: 'montserrat',
			label: 'Montserrat',
			stack: '"Montserrat", "Helvetica Neue", Arial, sans-serif'
		}
	] as const;

	type FontKey = (typeof fontOptions)[number]['key'];

	// Theme definitions with recommended colors for reduced eye strain
	const themes = {
		day: {
			name: 'Day',
			bg: '#FAFAFA', // Optimal light background
			text: '#222222', // Optimal light text
			bgContainer: '#FFFFFF',
			border: '#E5E7EB',
			muted: '#6B7280'
		},
		night: {
			name: 'Night',
			bg: '#121212', // Optimal dark background (recommended)
			text: '#E6E6E6', // Optimal dark text (recommended)
			bgContainer: '#1E1E1E',
			border: '#2D2D2D',
			muted: '#B3B3B3'
		}
	} as const;

	type ThemeKey = keyof typeof themes;

	let theme: ThemeKey = 'night'; // Optimal dark theme (recommended)
	let fontSize = 17; // Recommended size for reduced eye strain
	let fontFamily: FontKey = fontOptions[0].key; // Roboto
	let lineHeight = 1.6; // Recommended line height
	let currentPage = 1;
	let showControls = false;

	let documentData: Document | null = data?.document ?? null;

	$: documentData = data?.document ?? null;
	$: selectedFont = fontOptions.find((font) => font.key === fontFamily) ?? fontOptions[0];
	$: pages = groupContentByPage(documentData?.content ?? []);
	$: totalPages = documentData?.metadata?.page_count || pages.length || 1;
	$: currentPage = clamp(currentPage, 1, totalPages);
	$: displayedBlocks = pages[currentPage - 1] ?? [];
	$: progressPercent = Math.min(100, Math.max(0, (currentPage / totalPages) * 100));
	$: currentTheme = themes[theme];
	$: themeStyles = `background: ${currentTheme.bg}; color: ${currentTheme.text}; --bg-container: ${currentTheme.bgContainer}; --border-color: ${currentTheme.border}; --muted-color: ${currentTheme.muted};`;

	function groupContentByPage(content: TextBlock[]) {
		const grouped = new Map<number, TextBlock[]>();

		for (const block of content) {
			const pageBlocks = grouped.get(block.page_num) ?? [];
			pageBlocks.push(block);
			grouped.set(block.page_num, pageBlocks);
		}

		return Array.from(grouped.entries())
			.sort(([a], [b]) => a - b)
			.map(([, blocks]) => blocks.sort((a, b) => a.position - b.position));
	}

	function clamp(value: number, min: number, max: number) {
		return Math.min(Math.max(value, min), max);
	}

	function nextPage() {
		if (currentPage < totalPages) currentPage += 1;
	}

	function previousPage() {
		if (currentPage > 1) currentPage -= 1;
	}

	function goBack() {
		goto('/dashboard');
	}

	function toggleControls() {
		showControls = !showControls;
	}
</script>

<svelte:head>
	<title>{documentData?.title ? `${documentData.title} — Lector` : 'Lector'}</title>
	<link rel="preconnect" href="https://fonts.googleapis.com" />
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="anonymous" />
	<link
		rel="stylesheet"
		href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;500&family=Montserrat:wght@400;600&family=Merriweather:wght@400;700&display=swap"
	/>
</svelte:head>

<ProtectedRoute>
	<div class="h-screen" style={themeStyles}>
		<div class="mx-auto flex h-full max-w-6xl flex-1 flex-col px-4 py-6">
			<div
				class="flex h-full flex-col overflow-hidden rounded-3xl border shadow-xl"
				style={`background-color: var(--bg-container); border-color: var(--border-color);`}
			>
				<header
					class="flex items-center justify-between border-b px-4 py-3"
					style={`border-color: var(--border-color);`}
				>
					<div class="flex items-center gap-3">
						<button
							class="flex h-11 w-11 items-center justify-center rounded-2xl border text-sm font-semibold transition hover:opacity-80"
							style={`background-color: var(--bg-container); border-color: var(--border-color); color: var(--muted-color);`}
							on:click={goBack}
							aria-label="Back to library"
						>
							<ArrowLeft class="h-5 w-5" />
						</button>
						<div class="text-left">
							<h1
								class="text-lg leading-tight font-semibold"
								style={`color: ${currentTheme.text};`}
							>
								{documentData?.title ?? 'Untitled document'}
							</h1>
							<p class="text-sm" style={`color: var(--muted-color);`}>
								{documentData?.metadata?.author ?? documentData?.original_name ?? 'Unknown author'}
							</p>
						</div>
					</div>
					<button
						class="flex items-center gap-2 rounded-2xl border px-3 py-2 text-sm font-semibold transition hover:opacity-80"
						style={`background-color: var(--bg-container); border-color: var(--border-color); color: ${currentTheme.text};`}
						type="button"
						on:click={toggleControls}
						aria-expanded={showControls}
					>
						<Type class="h-5 w-5" />
						<span>Aa</span>
					</button>
				</header>

				<main class="flex min-h-0 flex-1 flex-col">
					<section class="flex min-h-0 flex-1 flex-col">
						<div
							class="flex flex-1 flex-col overflow-y-auto px-6 py-6"
							style={`font-family: ${selectedFont.stack}; font-size: ${fontSize}px; line-height: ${lineHeight}; background-color: ${currentTheme.bg}; color: ${currentTheme.text};`}
							aria-live="polite"
						>
							{#if documentData && displayedBlocks.length}
								<div class="flex flex-col">
									{#each displayedBlocks as block (block.page_num + '-' + block.position)}
										{#if block.type === 'heading'}
											<h2
												class="mb-4 leading-tight font-semibold"
												style={`font-size: ${Math.min(fontSize + 10, 36)}px; color: ${currentTheme.text};`}
											>
												{block.content}
											</h2>
										{:else}
											<p class="mb-4 text-inherit">{block.content}</p>
										{/if}
									{/each}
								</div>
							{:else if documentData}
								<div
									class="flex flex-1 items-center justify-center text-center text-sm"
									style={`color: var(--muted-color);`}
								>
									No content on this page.
								</div>
							{:else}
								<div
									class="flex flex-1 items-center justify-center text-center text-sm"
									style={`color: var(--muted-color);`}
								>
									Loading book…
								</div>
							{/if}
						</div>
					</section>

					{#if showControls}
						<TextPreference
							{theme}
							{fontSize}
							{fontFamily}
							{fontOptions}
							{themes}
							on:themeChange={(event) => (theme = event.detail as ThemeKey)}
							on:fontSizeChange={(event) => (fontSize = Number(event.detail))}
							on:fontFamilyChange={(event) => (fontFamily = event.detail as FontKey)}
						/>
					{/if}

					<footer
						class="flex flex-col gap-3 border-t px-5 py-4 sm:flex-row sm:items-center sm:justify-between"
						style={`border-color: var(--border-color);`}
					>
						<div class="flex flex-1 flex-col gap-2">
							<div
								class="h-2 w-full overflow-hidden rounded-full"
								style={`background-color: var(--border-color);`}
							>
								<span
									class="block h-full rounded-full transition-all"
									style={`width: ${progressPercent}%; background: linear-gradient(to right, #60A5FA, #3B82F6);`}
								></span>
							</div>
							<p class="text-sm" style={`color: var(--muted-color);`}>
								<span class="font-semibold">{progressPercent.toFixed(0)}%</span> read · page {currentPage}
								of {totalPages}
							</p>
						</div>
						<div class="flex gap-2">
							<button
								type="button"
								class="inline-flex items-center gap-2 rounded-xl border px-4 py-2 text-sm font-semibold transition hover:opacity-80 disabled:opacity-50"
								style={`background-color: var(--bg-container); border-color: var(--border-color); color: ${currentTheme.text};`}
								on:click={previousPage}
								disabled={currentPage === 1}
							>
								<ChevronLeft class="h-4 w-4" />
								Previous
							</button>
							<button
								type="button"
								class="inline-flex items-center gap-2 rounded-xl border px-4 py-2 text-sm font-semibold transition hover:opacity-80 disabled:opacity-50"
								style={`background-color: var(--bg-container); border-color: var(--border-color); color: ${currentTheme.text};`}
								on:click={nextPage}
								disabled={currentPage === totalPages}
							>
								Next
								<ChevronRight class="h-4 w-4" />
							</button>
						</div>
					</footer>
				</main>
			</div>
		</div>
	</div>
</ProtectedRoute>
