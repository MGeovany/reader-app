<script lang="ts">
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';
	import { isAuthenticated, currentUser, logout } from '$lib/stores/auth';
	import { documents, loadDocuments, documentsLoading } from '$lib/stores/documents';
	import ProtectedRoute from '$lib/components/ProtectedRoute.svelte';
	import {
		DoorOpen,
		LayoutPanelLeft,
		LibraryBig,
		MonitorUp,
		Plus,
		SlidersHorizontal,
		User
	} from '@lucide/svelte';

	let showProfile = false;

	onMount(() => {
		if ($isAuthenticated) {
			loadDocuments();
		}
	});

	async function handleLogout() {
		await logout();
		goto('/login');
	}

	function formatBytes(bytes: number) {
		if (!bytes) return '0 B';
		const units = ['B', 'KB', 'MB', 'GB'];
		const exponent = Math.min(Math.floor(Math.log(bytes) / Math.log(1024)), units.length - 1);
		const value = bytes / Math.pow(1024, exponent);
		return `${value.toFixed(exponent === 0 ? 0 : 1)} ${units[exponent]}`;
	}

	$: documentCount = $documents?.length ?? 0;
	$: totalBytes = ($documents ?? []).reduce(
		(sum: number, doc: any) => sum + (doc?.file_size ?? 0),
		0
	);
</script>

<ProtectedRoute>
	<div class="min-h-screen bg-slate-50 text-slate-900">
		<aside
			class="peer/sidebar group/sidebar fixed inset-y-0 left-0 z-10 flex w-16 flex-col border-r border-slate-200 bg-white/80 backdrop-blur transition-[width] duration-200 hover:w-64 supports-[backdrop-filter]:bg-white/60"
		>
			<div class="flex items-center gap-3 px-4 py-4">
				<div
					class="flex h-9 w-9 shrink-0 items-center justify-center rounded-xl border border-slate-200 bg-slate-50 text-sm font-semibold text-slate-900"
				>
					L
				</div>
				<div
					class="min-w-0 opacity-0 transition-opacity duration-200 group-hover/sidebar:opacity-100"
				>
					<p class="text-sm leading-5 font-semibold text-slate-900">Lector</p>
					<p class="text-xs text-slate-500">Dashboard</p>
				</div>
			</div>

			<nav class="mt-2 flex flex-1 flex-col gap-1 px-2">
				<a
					href="/dashboard"
					class="flex items-center gap-3 rounded-xl px-3 py-2 text-sm font-medium text-slate-900 hover:bg-slate-100"
					aria-current="page"
				>
					<LayoutPanelLeft class="h-5 w-5 shrink-0" />
					<span
						class="min-w-0 opacity-0 transition-opacity duration-200 group-hover/sidebar:opacity-100"
						>Overview</span
					>
				</a>

				<a
					href="#library"
					class="flex items-center gap-3 rounded-xl px-3 py-2 text-sm font-medium text-slate-700 hover:bg-slate-100 hover:text-slate-900"
				>
					<LibraryBig class="h-5 w-5 shrink-0" />
					<span
						class="min-w-0 opacity-0 transition-opacity duration-200 group-hover/sidebar:opacity-100"
						>Library</span
					>
				</a>

				<button
					on:click={() => (showProfile = !showProfile)}
					class="flex w-full items-center gap-3 rounded-xl px-3 py-2 text-sm font-medium text-slate-700 hover:bg-slate-100 hover:text-slate-900"
				>
					<User class="h-5 w-5 shrink-0" />
					<span
						class="min-w-0 opacity-0 transition-opacity duration-200 group-hover/sidebar:opacity-100"
						>Profile</span
					>
				</button>

				<a
					href="#settings"
					class="flex items-center gap-3 rounded-xl px-3 py-2 text-sm font-medium text-slate-700 hover:bg-slate-100 hover:text-slate-900"
				>
					<SlidersHorizontal class="h-5 w-5 shrink-0" />
					<span
						class="min-w-0 opacity-0 transition-opacity duration-200 group-hover/sidebar:opacity-100"
						>Settings</span
					>
				</a>
			</nav>

			<div class="px-2 pb-4">
				<button
					on:click={handleLogout}
					class="flex w-full items-center gap-3 rounded-xl px-3 py-2 text-sm font-medium text-slate-700 hover:bg-slate-100 hover:text-slate-900"
				>
					<DoorOpen class="h-5 w-5 shrink-0" />
					<span
						class="min-w-0 opacity-0 transition-opacity duration-200 group-hover/sidebar:opacity-100"
						>Sign out</span
					>
				</button>
			</div>
		</aside>

		<main class="min-h-screen pl-16 transition-[padding] duration-200 peer-hover/sidebar:pl-64">
			<div class="mx-auto flex max-w-6xl flex-col gap-6 px-6 py-8">
				<header class="flex flex-col gap-3 sm:flex-row sm:items-end sm:justify-between">
					<div>
						<h1 class="text-2xl font-semibold tracking-tight text-slate-900">Dashboard</h1>
						<p class="mt-1 text-sm text-slate-600">
							{$currentUser?.user_metadata?.name || $currentUser?.email || 'User'}
						</p>
					</div>
					<div class="flex items-center gap-2">
						<button
							type="button"
							class="inline-flex items-center gap-2 rounded-xl border border-slate-200 bg-white px-3 py-2 text-sm font-medium text-slate-900 hover:bg-slate-50"
						>
							<MonitorUp class="h-4 w-4" />
							Upload PDF
						</button>
						<button
							type="button"
							class="inline-flex items-center gap-2 rounded-xl border border-slate-900 bg-slate-900 px-3 py-2 text-sm font-semibold text-white hover:bg-slate-800"
						>
							<Plus class="h-4 w-4" />
							New document
						</button>
					</div>
				</header>

				<section class="grid grid-cols-1 gap-3 sm:grid-cols-3">
					<div class="rounded-2xl border border-slate-200 bg-white p-5">
						<p class="text-sm text-slate-600">Documents</p>
						<p class="mt-2 text-2xl font-semibold text-slate-900">{documentCount}</p>
					</div>
					<div class="rounded-2xl border border-slate-200 bg-white p-5">
						<p class="text-sm text-slate-600">Storage</p>
						<p class="mt-2 text-2xl font-semibold text-slate-900">{formatBytes(totalBytes)}</p>
					</div>
					<div class="rounded-2xl border border-slate-200 bg-white p-5">
						<p class="text-sm text-slate-600">Status</p>
						<p class="mt-2 text-sm font-medium text-slate-900">
							{#if $documentsLoading}Syncing…{:else}Up to date{/if}
						</p>
					</div>
				</section>

				<section id="library" class="rounded-2xl border border-slate-200 bg-white">
					<div class="flex items-center justify-between gap-4 border-b border-slate-200 px-5 py-4">
						<div>
							<h2 class="text-sm font-semibold text-slate-900">Library</h2>
							<p class="mt-1 text-sm text-slate-600">Your documents, ready to read.</p>
						</div>
					</div>

					{#if $documentsLoading}
						<div class="px-5 py-10 text-sm text-slate-600">Loading documents…</div>
					{:else if ($documents?.length ?? 0) === 0}
						<div class="px-5 py-10 text-center">
							<p class="text-sm font-medium text-slate-900">No documents yet</p>
							<p class="mt-1 text-sm text-slate-600">Upload your first PDF to get started.</p>
							<div class="mt-5 flex justify-center">
								<button
									type="button"
									class="rounded-xl border border-slate-900 bg-slate-900 px-4 py-2 text-sm font-semibold text-white hover:bg-slate-800"
								>
									Upload PDF
								</button>
							</div>
						</div>
					{:else}
						<div class="overflow-x-auto">
							<table class="w-full text-left text-sm">
								<thead
									class="border-b border-slate-200 bg-slate-50/60 text-xs font-semibold tracking-wide text-slate-600 uppercase"
								>
									<tr>
										<th class="px-5 py-3">Title</th>
										<th class="px-5 py-3">Pages</th>
										<th class="px-5 py-3">Size</th>
										<th class="px-5 py-3 text-right">Actions</th>
									</tr>
								</thead>
								<tbody class="divide-y divide-slate-200">
									{#each $documents as document}
										<tr class="hover:bg-slate-50/70">
											<td class="px-5 py-4">
												<p class="font-medium text-slate-900">{document.title}</p>
												<p class="mt-1 text-xs text-slate-500">{document.original_name}</p>
											</td>
											<td class="px-5 py-4 text-slate-700">
												{document.metadata?.page_count ?? '—'}
											</td>
											<td class="px-5 py-4 text-slate-700">{formatBytes(document.file_size)}</td>
											<td class="px-5 py-4">
												<div class="flex justify-end gap-2">
													<button
														type="button"
														class="rounded-lg border border-slate-200 bg-white px-3 py-1.5 text-sm font-medium text-slate-900 hover:bg-slate-50"
													>
														Read
													</button>
													<button
														type="button"
														class="rounded-lg border border-rose-200 bg-rose-50 px-3 py-1.5 text-sm font-medium text-rose-700 hover:bg-rose-100"
													>
														Delete
													</button>
												</div>
											</td>
										</tr>
									{/each}
								</tbody>
							</table>
						</div>
					{/if}
				</section>

				<section id="settings" class="rounded-2xl border border-slate-200 bg-white p-5">
					<h2 class="text-sm font-semibold text-slate-900">Settings</h2>
					<p class="mt-1 text-sm text-slate-600">Coming soon.</p>
				</section>
			</div>
		</main>
	</div>
</ProtectedRoute>
