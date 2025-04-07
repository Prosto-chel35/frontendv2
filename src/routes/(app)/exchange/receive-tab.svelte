<script lang="ts">
	import { ChevronUp } from 'lucide-svelte';

	type Props = {
		onNextTab: () => void;
		title?: string;
		onPrevTab: () => void;
	};

	let { onNextTab, onPrevTab, title = $bindable('') }: Props = $props();
	let isNextEnabled = $state(false);

	let categories = $state([
		{
			name: 'Жанр',
			expanded: false,
			selected: false,
			children: [
				{ name: 'История', selected: false },
				{ name: 'Детектив', selected: false },
				{ name: 'Приключения', selected: false },
				{ name: 'Детские книги', selected: false },
				{ name: 'Мемуары', selected: false },
				{ name: 'Психология', selected: false },
				{ name: 'Фантастика', selected: false },
				{ name: 'Эзотерика', selected: false },
				{ name: 'Фэнтези', selected: false },
				{ name: 'Поэзия', selected: false },
				{ name: 'Драма', selected: false },
				{ name: 'Классика', selected: false },
				{ name: 'Ужасы', selected: false },
				{ name: 'Роман', selected: false }
			]
		},
		{
			name: 'Область наук',
			expanded: false,
			selected: false,
			children: [
				{ name: 'Философия', selected: false },
				{ name: 'Физика', selected: false },
				{ name: 'Химия', selected: false },
				{ name: 'Биология', selected: false },
				{ name: 'Математика', selected: false },
				{ name: 'Информатика', selected: false },
				{ name: 'Лингвистика', selected: false },
				{ name: 'Психология', selected: false },
				{ name: 'Экономика', selected: false },
				{ name: 'История науки', selected: false }
			]
		},
		{
			name: 'Обложка',
			expanded: false,
			selected: false,
			children: [
				{ name: 'Твёрдая', selected: false },
				{ name: 'Мягкая', selected: false },
				{ name: 'Интегральная', selected: false },
				{ name: 'Кожаный переплёт', selected: false },
				{ name: 'Суперобложка', selected: false }
			]
		},
		{
			name: 'Лауреат',
			expanded: false,
			selected: false,
			children: [
				{ name: 'Нобелевская премия', selected: false },
				{ name: 'Букеровская премия', selected: false },
				{ name: 'Пулитцеровская премия', selected: false },
				{ name: 'Гонкуровская премия', selected: false },
				{ name: 'Литературная премия «Ясная Поляна»', selected: false },
				{ name: 'Литературная премия «Большая книга»', selected: false },
				{ name: 'Лауреат премии «Хьюго»', selected: false },
				{ name: 'Лауреат премии «Небьюла»', selected: false }
			]
		},
		{
			name: 'Экранизация',
			expanded: false,
			selected: false,
			children: [
				{ name: 'Есть', selected: false },
				{ name: 'Нет', selected: false }
			]
		},
		{
			name: 'Язык издателя',
			expanded: false,
			selected: false,
			children: [
				{ name: 'Русский', selected: false },
				{ name: 'Английский', selected: false },
				{ name: 'Немецкий', selected: false },
				{ name: 'Французский', selected: false },
				{ name: 'Китайский', selected: false },
				{ name: 'Испанский', selected: false },
				{ name: 'Итальянский', selected: false },
				{ name: 'Японский', selected: false }
			]
		}
	]);

	function toggleCategory(index: number) {
		categories[index].expanded = !categories[index].expanded;
	}

	function toggleSelection(parentIndex: number, childIndex: number) {
		categories[parentIndex].children[childIndex].selected =
			!categories[parentIndex].children[childIndex].selected;
		categories[parentIndex].selected = categories[parentIndex].children.some(
			(child) => child.selected
		); // Обновляем родителя
	}

	function clearSelection() {
		categories.map((category) => {
			category.selected = false;
			category.children.map((child) => {
				child.selected = false;
				return { ...child };
			});
			return { ...category };
		});
	}

	function validateAndProceed() {
		const cats = categories;
		console.log('Текущие категории:', cats);
		const genre = cats.find((cat) => cat.name === 'Жанр');
		console.log("Найдена категория 'Жанр':", genre);

		if (!genre) {
			alert("Категория 'Жанр' не найдена!");
			return;
		}
		const isSelected = genre.children.some((child) => child.selected);
		console.log('Выбраны ли подкатегории жанра:', isSelected);

		if (!isSelected) {
			alert("Выберите хотя бы одну категорию в 'Жанр'!");
			return;
		}

		console.log('Категория выбрана, выполняем переход...');

		onNextTab();
	}

	$effect(() => {
		isNextEnabled =
			categories
				.find((cat) => cat.name === 'Жанр')
				?.children.some((child) => child.selected) ?? false;
	});
</script>

<div class="flex w-full rounded-lg bg-walnut-accent p-6 shadow  text-black">
	<div class="mx-auto flex h-[400px] w-1/2 flex-col 	">
		<div class="mb-4 flex items-center justify-between">
			<h2 class="text-xl font-semibold">Категории</h2>
			<button onclick={clearSelection} class="rounded hover:bg-walnut-light border bg-walnut-accent p-2 px-4 text-black"
				>Снять выделение</button
			>
		</div>

		<div class="flex-grow  bg-walnut-accent overflow-y-auto rounded border p-2 pr-2">
			{#each categories as category, parentIndex}
				<div class="category-item mb-2 rounded border bg-walnut-accent p-2">
					<button
						onclick={() => toggleCategory(parentIndex)}
						class="flex w-full flex-row items-center gap-3 text-left font-bold"
						style="font-weight: {category.selected ? 'bold' : 'normal'}"
					>
						<ChevronUp
							class="size-5 transition-transform data-[selected]:rotate-180"
							data-selected={category.expanded ? '' : undefined}
						/>
						{category.name}
					</button>
					{#if category.expanded}
						{#each category.children as child, childIndex}
							<div class="ml-6">
								<input
									type="checkbox"
									checked={child.selected}
									onchange={() => toggleSelection(parentIndex, childIndex)}
								/>
								<span>{child.name}</span>
							</div>
						{/each}
					{/if}
				</div>
			{/each}
		</div>
		<div class="mt-4 flex justify-between"></div>
	</div>
</div>

<div class="mt-4 flex justify-between">
	<button onclick={onPrevTab} class="rounded hover:bg-walnut-light bg-walnut-accent p-2 px-20 text-black">Назад</button>
	<button
		onclick={() => validateAndProceed()}
		class="{isNextEnabled
			? 'bg-walnut-accent'
			: 'cursor-not-allowed bg-gray-500'} rounded p-2 px-20 text-black"
		disabled={!isNextEnabled}
	>
		Далее
	</button>
</div>

<style>
	button {
		cursor: pointer;
	}
	.category-item {
		transition:
			background-color 0.3s ease,
			box-shadow 0.3s ease;
		border-radius: 0.375rem;
	}

	.category-item:hover {
		background-color: #E8E3D0;
		box-shadow: 0 0 0 2px rgb(255, 255, 255);
	}

	input {
		transition:
			border-color 0.3s ease,
			box-shadow 0.3s ease;
		border-radius: 0.375rem;
	}

	input:focus {
		border-color: #eaeed3;
		box-shadow: 0 0 0 3px rgb(227, 233, 208);
		outline: none;
	}

	button {
		cursor: pointer;
		transition: background-color 0.3s ease;
	}

	
</style>
