<script setup lang="ts">
import { cva } from 'class-variance-authority';

const buttonVariants = cva(
  'inline-flex items-center justify-center rounded-md text-sm font-medium ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50',
  {
    variants: {
      variant: {
        default: 'bg-zinc-900 text-white hover:bg-zinc-800',
        destructive:
          'bg-red-600 text-white hover:bg-red-500',
        outline:
          'border border-zinc-300 bg-transparent hover:bg-zinc-100 text-zinc-900',
        ghost:
          'text-zinc-500 hover:text-zinc-900 bg-transparent',
        secondary:
          'bg-zinc-200 text-zinc-900 hover:bg-zinc-300',
      },
      size: {
        default: 'h-8 px-4 py-2',
        sm: 'h-7 rounded-md px-3 text-xs',
        lg: 'h-10 rounded-md px-8',
        icon: 'h-8 w-8',
        'icon-xs': 'h-5 w-5'
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'default',
    },
  },
)

const props = withDefaults(defineProps<{
  variant?: NonNullable<Parameters<typeof buttonVariants>[0]>['variant']
  size?: NonNullable<Parameters<typeof buttonVariants>[0]>['size']
  as?: string
}>(), {
  as: 'button',
})
</script>

<template>
  <NuxtLink :class="cn(buttonVariants({ variant, size }), $attrs.class ?? '')" v-if="props.as == 'NuxtLink'">
    <slot/>
  </NuxtLink>
  <component :is="as" :class="cn(buttonVariants({ variant, size }), $attrs.class ?? '')" v-else>
    <slot />
  </component>
</template>