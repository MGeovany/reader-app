<script lang="ts">
	import { goto } from '$app/navigation';
	import { loginWithGoogle, isAuthenticated, authError } from '$lib/stores/auth';
	import GoogleIcon from '$lib/components/shared/GoogleIcon.svelte';
	import { onMount } from 'svelte';

	let googleLoading = false;

	onMount(() => {
		if ($isAuthenticated) {
			goto('/');
		}
	});

	async function handleLoginWithGoogle() {
		googleLoading = true;
		try {
			await loginWithGoogle();
			// Redirect will be handled by auth state change
		} catch (err: any) {
			console.error('Google login failed:', err);
		} finally {
			googleLoading = false;
		}
	}
</script>

<div class="min-h-screen bg-slate-50">
	<div class="mx-auto flex min-h-screen max-w-md items-center px-4 py-12">
		<div class="w-full">
			<div
				class="rounded-2xl border border-slate-200 bg-white px-6 py-7 shadow-sm shadow-slate-100 sm:px-8 sm:py-8"
			>
				<div class="mb-6 flex items-center justify-between gap-4">
					<div class="text-left">
						<p class="text-xs font-semibold tracking-[0.2em] text-slate-500 uppercase">Lector</p>
						<h2 class="mt-2 text-2xl font-semibold text-slate-900">Sign in</h2>
						<p class="mt-1 text-sm text-slate-600">Sign in to your account to continue reading.</p>
					</div>
				</div>

				<div class="space-y-4">
					{#if $authError}
						<div
							class="rounded-lg border border-rose-100 bg-rose-50 px-3 py-2 text-sm text-rose-700"
						>
							{$authError}
						</div>
					{/if}

					<!-- Google Login -->
					<div class="space-y-3">
						<button
							type="button"
							on:click={handleLoginWithGoogle}
							disabled={googleLoading}
							class="flex w-full items-center justify-center gap-2 rounded-lg border border-slate-200 bg-white px-4 py-2 text-sm font-medium text-slate-900 transition hover:bg-slate-50 focus:ring-2 focus:ring-slate-900 focus:ring-offset-2 focus:outline-none disabled:cursor-not-allowed disabled:opacity-50"
						>
							<GoogleIcon class="h-4 w-4" />
							<span>{googleLoading ? 'Redirecting to Google…' : 'Continue with Google'}</span>
						</button>
					</div>
				</div>
			</div>
		</div>
	</div>
</div>
