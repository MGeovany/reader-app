<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	import { Moon, Sun } from '@lucide/svelte';

	type FontOption = {
		key: string;
		label: string;
		stack: string;
		recommended?: boolean;
	};

	type ThemeConfig = {
		name: string;
		bg: string;
		text: string;
		bgContainer: string;
		border: string;
		muted: string;
	};

	type Themes = {
		[key: string]: ThemeConfig;
	};

	type ThemeKey = keyof Themes;

	const MIN_FONT_SIZE = 15;
	const MAX_FONT_SIZE = 26;

	const dispatch = createEventDispatcher<{
		themeChange: ThemeKey;
		fontSizeChange: number;
		fontFamilyChange: FontOption['key'];
	}>();

	export let theme: ThemeKey = 'night'; // Optimal dark theme (recommended)
	export let fontSize = 17; // Recommended size for reduced eye strain
	export let fontFamily: FontOption['key'] = 'roboto';
	export let fontOptions: ReadonlyArray<FontOption> = [];
	export let themes: Themes = {};
	// Slider fill percentage to drive the iOS-like gradient
	$: sliderPercent = Math.min(
		100,
		Math.max(0, ((fontSize - MIN_FONT_SIZE) / (MAX_FONT_SIZE - MIN_FONT_SIZE)) * 100)
	);

	function setTheme(next: ThemeKey) {
		if (theme !== next) {
			theme = next;
			dispatch('themeChange', next);
		}
	}

	$: currentTheme = themes[theme] || themes['night'];
	$: mutedClass = currentTheme?.muted || '#B3B3B3';

	function setFontFamily(next: FontOption['key']) {
		if (fontFamily !== next) {
			fontFamily = next;
			dispatch('fontFamilyChange', next);
		}
	}

	function handleSizeInput(event: Event) {
		const next = Number((event.currentTarget as HTMLInputElement)?.value ?? fontSize);
		setFontSize(next);
	}

	function setFontSize(next: number) {
		const clamped = clamp(next, MIN_FONT_SIZE, MAX_FONT_SIZE);
		fontSize = clamped;
		dispatch('fontSizeChange', clamped);
	}

	function adjustFont(delta: number) {
		setFontSize(fontSize + delta);
	}

	function clamp(value: number, min: number, max: number) {
		return Math.min(Math.max(value, min), max);
	}
</script>

<section
	data-theme={theme}
	class="border-t px-5 py-5 transition"
	style={`border-color: ${currentTheme?.border || '#2D2D2D'}; background-color: ${currentTheme?.bgContainer || '#1E1E1E'}; color: ${currentTheme?.text || '#E6E6E6'};`}
>
	<div class="space-y-5">
		<div>
			<p class="text-xs font-semibold tracking-[0.18em] uppercase" style={`color: ${mutedClass};`}>
				Size
			</p>
			<div class="mt-2 flex items-center gap-3">
				<button
					type="button"
					class="size-button flex h-10 w-10 items-center justify-center rounded-full border text-base font-bold shadow-sm"
					style={`border-color: ${currentTheme?.border || '#2D2D2D'}; background-color: ${currentTheme?.bgContainer || '#1E1E1E'}; color: ${currentTheme?.text || '#E6E6E6'};`}
					on:click={() => adjustFont(-1)}
					aria-label="Decrease font size"
					disabled={fontSize <= MIN_FONT_SIZE}
				>
					-
				</button>
				<input
					type="range"
					min={MIN_FONT_SIZE}
					max={MAX_FONT_SIZE}
					step="1"
					value={fontSize}
					on:input={handleSizeInput}
					class="slider-ios"
					style={`--slider-percent:${sliderPercent}%;`}
					aria-label="Adjust font size"
				/>
				<button
					type="button"
					class="size-button flex h-10 w-10 items-center justify-center rounded-full border text-base font-bold shadow-sm"
					style={`border-color: ${currentTheme?.border || '#2D2D2D'}; background-color: ${currentTheme?.bgContainer || '#1E1E1E'}; color: ${currentTheme?.text || '#E6E6E6'};`}
					on:click={() => adjustFont(1)}
					aria-label="Increase font size"
					disabled={fontSize >= MAX_FONT_SIZE}
				>
					+
				</button>
			</div>
		</div>

		<div>
			<p class="text-xs font-semibold tracking-[0.18em] uppercase" style={`color: ${mutedClass};`}>
				Theme
			</p>
			<div class="mt-2 grid grid-cols-2 gap-3">
				{#each Object.entries(themes) as [key, config]}
					{@const isActive = theme === key}
					<button
						type="button"
						class="motion-pill flex items-center justify-center gap-2 rounded-full border px-3 py-3 text-sm font-semibold transition"
						class:shadow-lg={isActive}
						style={isActive
							? `border-color: #60A5FA; background-color: #DBEAFE; color: #1E293B; box-shadow: 0 12px 26px rgba(56,189,248,0.25);`
							: `border-color: ${currentTheme?.border || '#2D2D2D'}; background-color: ${currentTheme?.bg || '#121212'}; color: ${currentTheme?.text || '#E6E6E6'}; opacity: 0.8;`}
						on:click={() => setTheme(key)}
						aria-pressed={isActive}
						data-active={isActive}
					>
						{#if key === 'day'}
							<Sun class="h-4 w-4" />
						{:else if key === 'night'}
							<Moon class="h-4 w-4" />
						{/if}
						<span>{config.name}</span>
					</button>
				{/each}
			</div>
		</div>

		<div>
			<p class="text-xs font-semibold tracking-[0.18em] uppercase" style={`color: ${mutedClass};`}>
				Fonts
			</p>
			<div class="mt-3 grid grid-cols-2 gap-3 sm:grid-cols-4">
				{#each fontOptions as font}
					{@const isActive = fontFamily === font.key}
					<button
						type="button"
						class="motion-pill rounded-full border px-3 py-3 text-sm font-semibold transition"
						class:shadow-lg={isActive}
						style={isActive
							? `font-family: ${font.stack}; border-color: #60A5FA; background-color: #DBEAFE; color: #1E293B; box-shadow: 0 12px 26px rgba(56,189,248,0.25);`
							: `font-family: ${font.stack}; border-color: ${currentTheme?.border || '#2D2D2D'}; background-color: ${currentTheme?.bg || '#121212'}; color: ${currentTheme?.text || '#E6E6E6'}; opacity: 0.8;`}
						on:click={() => setFontFamily(font.key)}
						aria-pressed={isActive}
						data-active={isActive}
					>
						<span>{font.label}</span>
					</button>
				{/each}
			</div>
		</div>
	</div>
</section>

<style>
	section .slider-ios {
		--slider-fill: #38bdf8;
		--slider-shadow: 0 8px 18px rgba(56, 189, 248, 0.24);
		--slider-border: rgba(0, 0, 0, 0.1);
		--slider-thumb-border: rgba(0, 0, 0, 0.15);
	}

	section[data-theme='day'] .slider-ios {
		--slider-track: #e5e7eb;
		--slider-thumb: #ffffff;
		--slider-border: rgba(0, 0, 0, 0.1);
		--slider-thumb-border: rgba(0, 0, 0, 0.15);
	}

	section[data-theme='night'] .slider-ios {
		--slider-track: #1e1e1e;
		--slider-thumb: #121212;
		--slider-border: rgba(255, 255, 255, 0.1);
		--slider-thumb-border: rgba(255, 255, 255, 0.2);
	}

	.slider-ios {
		appearance: none;
		width: 100%;
		height: 14px;
		border-radius: 999px;
		background: linear-gradient(
			to right,
			var(--slider-fill) 0%,
			var(--slider-fill) var(--slider-percent, 50%),
			var(--slider-track) var(--slider-percent, 50%),
			var(--slider-track) 100%
		);
		border: 1px solid var(--slider-border);
		outline: none;
		box-shadow: inset 0 1px 2px rgba(0, 0, 0, 0.12);
		transition:
			background 300ms cubic-bezier(0.4, 0, 0.2, 1),
			border-color 300ms cubic-bezier(0.4, 0, 0.2, 1);
	}

	.slider-ios:focus-visible {
		box-shadow: 0 0 0 3px rgba(56, 189, 248, 0.35);
	}

	.slider-ios::-webkit-slider-thumb {
		appearance: none;
		height: 22px;
		width: 22px;
		border-radius: 50%;
		background: var(--slider-thumb);
		border: 1px solid var(--slider-thumb-border);
		box-shadow: var(--slider-shadow);
		transition:
			transform 300ms cubic-bezier(0.4, 0, 0.2, 1),
			box-shadow 300ms cubic-bezier(0.4, 0, 0.2, 1),
			border-color 300ms cubic-bezier(0.4, 0, 0.2, 1);
		cursor: grab;
	}

	.slider-ios::-webkit-slider-thumb:active {
		cursor: grabbing;
	}

	.slider-ios:hover::-webkit-slider-thumb {
		transform: scale(1.04);
		box-shadow: 0 10px 24px rgba(56, 189, 248, 0.32);
	}

	.slider-ios:active::-webkit-slider-thumb {
		transform: scale(0.96);
	}

	.slider-ios::-moz-range-track {
		height: 14px;
		border-radius: 999px;
		background: transparent;
		border: 1px solid var(--slider-border);
	}

	.slider-ios::-moz-range-progress {
		height: 14px;
		border-radius: 999px 0 0 999px;
		background: var(--slider-fill);
	}

	.slider-ios::-moz-range-thumb {
		height: 22px;
		width: 22px;
		border-radius: 50%;
		background: var(--slider-thumb);
		border: 1px solid var(--slider-thumb-border);
		box-shadow: var(--slider-shadow);
		transition:
			transform 300ms cubic-bezier(0.4, 0, 0.2, 1),
			box-shadow 300ms cubic-bezier(0.4, 0, 0.2, 1),
			border-color 300ms cubic-bezier(0.4, 0, 0.2, 1);
		cursor: grab;
	}

	.slider-ios::-moz-range-thumb:active {
		cursor: grabbing;
	}

	.slider-ios:hover::-moz-range-thumb {
		transform: scale(1.04);
		box-shadow: 0 10px 24px rgba(56, 189, 248, 0.32);
	}

	.slider-ios:active::-moz-range-thumb {
		transform: scale(0.96);
	}

	.size-button {
		aspect-ratio: 1;
		transition:
			transform 200ms cubic-bezier(0.4, 0, 0.2, 1),
			box-shadow 200ms cubic-bezier(0.4, 0, 0.2, 1),
			opacity 200ms ease;
	}

	.size-button:hover:not(:disabled) {
		transform: translateY(-2px);
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
	}

	.size-button:active:not(:disabled) {
		transform: translateY(0);
		box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
	}

	.size-button:disabled {
		opacity: 0.5;
		cursor: not-allowed;
	}

	.motion-pill {
		transition:
			transform 180ms cubic-bezier(0.2, 0.8, 0.25, 1),
			box-shadow 200ms ease,
			filter 200ms ease;
	}

	.motion-pill:hover {
		transform: translateY(-1px) scale(1.01);
	}

	.motion-pill:active {
		transform: translateY(0) scale(0.98);
	}

	.motion-pill[data-active='true'] {
		transform: translateY(-2px);
	}

	@media (prefers-reduced-motion: reduce) {
		.motion-pill {
			transition: none;
		}

		.motion-pill:hover,
		.motion-pill:active,
		.motion-pill[data-active='true'] {
			transform: none;
		}
	}
</style>
