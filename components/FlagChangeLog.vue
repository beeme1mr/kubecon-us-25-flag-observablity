<script setup lang="ts">

const props = defineProps({
  flagName: {
    type: String,
    default: 'recommendationServiceCacheFailure'
  },
  userName: {
    type: String,
    default: 'Sarah Chen'
  },
  userEmail: {
    type: String,
    default: 'sarah.chen@example.com'
  },
  userAvatar: {
    type: String,
    default: '' // Optional avatar URL
  },
  timestamp: {
    type: String,
    default: 'Oct 15, 2025, 10:03 AM'
  },
  oldValue: {
    type: String,
    default: 'off'
  },
  newValue: {
    type: String,
    default: 'on'
  },
  changeReason: {
    type: String,
    default: 'Testing new cache implementation in production'
  }
});

// Generate avatar initials if no avatar provided
const avatarInitials = props.userName
  .split(' ')
  .map(n => n[0])
  .join('')
  .toUpperCase();
</script>

<template>
  <div class="flag-changelog">
    <!-- Compact header with user and timestamp -->
    <div class="flex items-center justify-between mb-2">
      <div class="flex items-center gap-2">
        <div v-if="userAvatar" class="avatar">
          <img :src="userAvatar" :alt="userName" />
        </div>
        <div v-else class="avatar-initials">
          {{ avatarInitials }}
        </div>
        <div>
          <div class="font-semibold text-xs">{{ userName }}</div>
          <div class="opacity-60 text-2xs">{{ userEmail }}</div>
        </div>
      </div>
      <div class="flex items-center gap-1 text-2xs opacity-70">
        <div i-carbon:time text-xs />
        <span>{{ timestamp }}</span>
      </div>
    </div>

    <!-- Flag name and value change in one row -->
    <div class="flex items-center gap-2 mb-2">
      <code class="flag-name">{{ flagName }}</code>
      <div class="flex items-center gap-1.5">
        <span class="value-badge old-value">{{ oldValue }}</span>
        <div i-carbon:arrow-right text-purple-400 text-xs />
        <span class="value-badge new-value">{{ newValue }}</span>
      </div>
    </div>

    <!-- Change reason -->
    <div v-if="changeReason" class="change-reason">
      <div class="flex items-start gap-1.5">
        <div i-carbon:chat text-amber-400 text-xs mt-0.5 flex-shrink-0 />
        <div class="text-2xs opacity-70 italic">"{{ changeReason }}"</div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.flag-changelog {
  background: linear-gradient(135deg, rgba(141, 141, 255, 0.12), rgba(93, 93, 255, 0.06));
  border: 1px solid rgba(141, 141, 255, 0.3);
  border-radius: 6px;
  padding: 0.5rem 0.65rem;
  box-shadow: 0 4px 16px 0 rgba(60, 66, 110, 0.25);
}

.avatar {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  overflow: hidden;
  flex-shrink: 0;
}

.avatar img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.avatar-initials {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: linear-gradient(135deg, rgba(141, 141, 255, 0.5), rgba(93, 93, 255, 0.5));
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  font-size: 0.6rem;
  color: white;
  flex-shrink: 0;
}

.flag-name {
  font-size: 0.65rem;
  padding: 0.15rem 0.4rem;
  background: rgba(141, 141, 255, 0.15);
  border: 1px solid rgba(141, 141, 255, 0.3);
  border-radius: 3px;
  color: rgb(183, 185, 255);
}

.value-badge {
  font-size: 0.65rem;
  padding: 0.15rem 0.4rem;
  border-radius: 3px;
  font-weight: 600;
  font-family: 'Fira Code', monospace;
}

.old-value {
  background: rgba(127, 29, 29, 0.3);
  border: 1px solid rgba(239, 68, 68, 0.4);
  color: rgba(248, 113, 113, 0.9);
}

.new-value {
  background: rgba(21, 128, 61, 0.3);
  border: 1px solid rgba(34, 197, 94, 0.4);
  color: rgba(134, 239, 172, 0.9);
}

.change-reason {
  background: rgba(251, 146, 60, 0.08);
  border: 1px solid rgba(251, 146, 60, 0.25);
  border-radius: 3px;
  padding: 0.35rem 0.4rem;
}

.text-2xs {
  font-size: 0.625rem;
  line-height: 0.875rem;
}
</style>
