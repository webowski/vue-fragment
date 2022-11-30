<template>
	<div>
		<a-row :gutter="24" type="flex">
			<a-col :span="24" class="mb-24">
				<x-table
					entityName="evaluation-sheet-archive"
					:data="evaluationSheetArchive"
					:columns="columns"
					:actions="['download', 'delete']"
					@download="downloadItem"
					@delete="deleteItem"
				>
				</x-table>
			</a-col>
		</a-row>
	</div>
</template>

<script>
import { EVALUATION_SHEET_ARCHIVE } from '@/helpers/constants'
const { VUE_APP_API_MEDIA: MEDIA } = process.env

const columns = [
	{
		title: 'ID',
		dataIndex: 'id',
		filterable: false,
	},
	{
		title: 'Файл',
		dataIndex: 'evaluation_sheet',
	},
]

export default {
	data() {
		return {
			columns,
			sheetPath: '',
		}
	},
	async mounted() {
		await this.getEvaluationSheetArchive()
	},
	computed: {
		evaluationSheetArchive() {
			return this.$store.state.app['evaluation-sheet-archive']
		},
	},
	methods: {
		async getEvaluationSheetArchive() {
			try {
				await this.$store.dispatch('app/getEntities', {
					entityName: EVALUATION_SHEET_ARCHIVE,
				})
			} catch (e) {
				console.log(e)
			}
		},
		async deleteItem(id) {
			this.loading = true
			try {
				await this.$store.dispatch('app/deleteItem', {
					entityName: EVALUATION_SHEET_ARCHIVE,
					id,
				})
				this.$toast.success('Успешно удалено')
			} catch (e) {
				console.log(e)
				this.$errors.showOnce(e.response.data)
			} finally {
				this.loading = false
			}
		},
		async downloadItem(id) {
			this.loading = true
			try {
				let response = await this.$store.dispatch('app/getItem', {
					entityName: EVALUATION_SHEET_ARCHIVE,
					id,
				})
				this.sheetPath = `${MEDIA}/${response.data.evaluation_sheet}`
				this.downloadUri(this.sheetPath)
			} catch (e) {
				console.log(e)
				this.$errors.showOnce(e.response.data)
			} finally {
				this.loading = false
			}
		},
		downloadUri(uri) {
			const link = document.createElement('a')
			const filename = uri.replace(/^.*[\\\/]/, '')
			link.href = uri
			link.download = filename
			link.click()
		},
	},
}
</script>
