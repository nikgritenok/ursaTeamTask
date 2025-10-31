<template>
  <div class="d-flex flex-column ga-2 align-center">
    <span class="text-h4 text-center"> Генератор HTML страниц </span>

    <v-form @submit.prevent="downloadHtmlFile">
      <v-text-field
        v-model="createPageModalValue.pageTitle"
        label="Наименование страницы"
        variant="outlined"
        density="compact"
        :rules="[requiredRule]"
        required
      />

      <v-text-field
        v-model="createPageModalValue.pageDescription"
        label="Описание страницы"
        variant="outlined"
        density="compact"
        :rules="[requiredRule]"
        required
      />

      <v-textarea
        v-model="createPageModalValue.h1Title"
        label="H1 заголовок"
        variant="outlined"
        density="compact"
        :rules="[requiredRule]"
        required
      />

      <v-textarea
        v-model="createPageModalValue.paragraphContent"
        label="Контент абзаца"
        variant="outlined"
        density="compact"
        required
        :rules="[requiredRule]"
      />

      <v-btn
        type="submit"
        color="primary"
        size="large"
        block
        :disabled="!isFormValid"
        text="Скачать HTML страницу"
      />
    </v-form>
  </div>
</template>

<script setup lang="ts">
// Libs
import { computed, reactive } from 'vue'

const createPageModalValue = reactive({
  pageTitle: '',
  pageDescription: '',
  h1Title: '',
  paragraphContent: '',
})

const requiredRule = (value: string): boolean | string => {
  return !!value || 'Это обязательное поле'
}

const isFormValid = computed(
  () =>
    createPageModalValue.pageTitle &&
    createPageModalValue.pageDescription &&
    createPageModalValue.h1Title &&
    createPageModalValue.paragraphContent,
)

const generateHtmlContent = () => {
  return `<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="${createPageModalValue.pageDescription}">
    <title>${createPageModalValue.pageTitle}</title>
</head>
<body>
    <h1>${createPageModalValue.h1Title}</h1>
    <p>${createPageModalValue.paragraphContent}</p>
</body>
</html>`
}

const downloadHtmlFile = () => {
  if (!isFormValid.value) return

  const htmlContent = generateHtmlContent()
  const blob = new Blob([htmlContent], { type: 'text/html' })
  const url = URL.createObjectURL(blob)

  const link = document.createElement('a')
  link.href = url
  link.download = `${createPageModalValue.pageTitle.replace(/[^a-z0-9]/gi, '_').toLowerCase()}.html`
  document.body.appendChild(link)
  link.click()
  document.body.removeChild(link)

  URL.revokeObjectURL(url)
}
</script>
