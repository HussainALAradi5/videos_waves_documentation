# Web client reference (`videos_waves_client`)

## Routes (`src/app`)

| Route | View | Access |
| --- | --- | --- |
| `/` | Feed: For you / Following tabs, search, sort, infinite grid, hover preview, double-tap like | Public (Following needs sign-in) |
| `/videos/[videoId]` | Player, likes, owner follow, owner ⋯ menu (edit, delete), comments with replies | Public |
| `/upload` | Several videos at once: drop zone, one draft card per file with progress, Publish | Signed in |
| `/search` | People search with Follow | Public |
| `/u/[userName]` | Profile: counts, Follow, Message, ⋯ (block, restrict), videos, followers and following dialogs | Public |
| `/profile` | Your profile | Signed in |
| `/messages`, `/messages/[conversationId]` | Inbox and thread (two panes on desktop, stacked on phones) | Signed in |
| `/settings` | Account and avatar, preferences by key, sessions, blocked and restricted lists, admin request | Signed in |
| `/admin/requests` | Review admin requests | Admin |
| `/admin/users` | Every account in a table: role and status badges, search, role and status filters, pagination, Activate / Deactivate (not for admins), Export CSV, and Import CSV with a preview and a one-time password download | Admin |
| `/login`, `/register` | Auth (register asks for gender) | Guests only (`GuestOnly`) |

`app/template.tsx` animates each page in (enter only). The shell has a side nav on desktop and a bottom tab bar below 768 px, with the unread badge on Messages.

## Services (`src/services`), through one Axios `apiClient`

| Service | Functions |
| --- | --- |
| `authService` | `register`, `login`, `logout`, `logoutEverywhere`, `listSessions`, `revokeSession` |
| `token-refresh.service` | `refreshAccessToken`, `getFreshAccessToken`: one refresh across tabs (Web Locks); clears the session only on 401 |
| `userService` | `getMe`, `updateMe`, `updateAvatar`, `changePassword`, `deactivate`, `getSettings`, `updateSettings` |
| `socialService` | `getProfile`, `search`, `followers`, `following`, `follow`, `unfollow`, `block`, `unblock`, `restrict`, `unrestrict`, `blocked`, `restricted` |
| `videoService` | `list`, `getById`, `create`, `update`, `remove`, `recordView`, `like`, `unlike` |
| `commentService` | `list`, `listReplies`, `create`, `update`, `remove`, `restore`, `approve` |
| `mediaUploadService` | Signature → direct upload with `onUploadProgress` → register; `discard` |
| `conversationService` | `list`, `get`, `unreadCount`, `startDirect`, `createGroup`, `update`, `hide`, `accept`, `markRead`, `addMembers`, `removeMember`, `setRole` |
| `messageService` | `list` (cursor), `send`, `edit`, `remove` (scope), `react`, `unreact` |
| `socketClient` | `connect`, `auth` (fresh token on reconnect), `disconnect`, `get` |
| `adminRequestService`, `tokenStorage` | Unchanged from round 1 |

`api-request.ts` unwraps the envelope (`getData`, `getPage`, `getCursorPage`, `postData`, `putData`, `patchData`, `deleteData`); every failure becomes `ApiError { status, code, message, details, requestId }`.

## Hooks (`src/hooks`): all state and data logic

| Group | Hooks |
| --- | --- |
| `common` | `usePagedList`, `useCursorList`, `useAsyncAction` (busy buttons), `useGenericForm`, `useInlineEditor`, `useMultiFileQueue`, `useMultiFileDropzone`, `useMotionVariants` (reduced motion), `useColorMode`, `useDebouncedValue`, `useInViewTrigger`, `useNavigation`, `useShareLink` |
| `users/auth` | `useAuthSession`, `useAuth`, `useLogin`, `useRegister`, `useLogout`, `useRequireAuth`, `useGuestOnly`, `useSessions` |
| `users/social` | `useProfile`, `useFollow`, `useRelationActions`, `useRelationList`, `usePeople`, `useUserSearch` |
| `users/settings` | `useSettings`, `useUpdateSetting` (optimistic), `useApplySettings` (theme) |
| `users/user`, `users/admin-request` | `useAvatarEditor`, `useProfileActions`, `useProfileDialogs`; `useAdminRequests`, `useAdminRequestActions` |
| `videos/video` | `useVideoFeed`, `useFeedFilters`, `useVideo`, `useVideoDetails`, `useVideoLike`, `useRecordView`, `useVideoOwnerActions`, `useMultiVideoUpload`, `useDoubleTap`, `useHoverPreview` |
| `videos/comment` | `useComments`, `useCommentItem` (who may edit, delete, approve), `useCommentActions` (delete with Undo), `useCommentComposer` |
| `chat/*` | `useInbox`, `useConversations`, `useConversation`, `useConversationActions`, `useStartChat`, `usePeoplePicker`, `useUnreadCount`, `useMessages`, `useMessageActions`, `useChatComposer`, `useVoiceRecorder`, `useAudioPlayer`, `useTypingSignal`, `useStickToBottom`, `useChatSocket`, `useTypingUsers` |

Caches: `utilities/cache/video-cache.utility.ts` (`patchVideoInCache`), `infinite-cache.utility.ts`, and `chat-cache.utility.ts`, which applies socket events (`upsertMessage`, `replaceMessage`, `applyReaction`, `applyRead`, `applyPresence`, `refreshConversation`) without refetching.

## Generic components (`src/components/common`)

| Group | Components |
| --- | --- |
| Buttons | `GenericButton`, `GenericIconButton`: spinner and no repeat clicks while the action runs |
| Dropdown | `SelectDropdown`, `MultiSelectDropdown` (chips and search), `ActionMenu` (the ⋯ menu, destructive entries last), `DropdownOptionRow`, `SelectedChips` |
| Uploads | `MultiFileDropzone`, `FilePreviewGrid`, `FilePreviewTile` (thumbnail, size, progress ring, remove, retry) |
| Forms | `GenericForm`, `GenericField`, `InlineEditor`, `PasswordInput`, `ChoiceCards` |
| Feedback | `GenericProgressBar`, `ProgressRing`, `QueryState`, `SkeletonGrid`, `SkeletonList`, `LoadingState`, `EmptyState`, `ErrorState` |
| Dialogs | `GenericModal`, `BottomSheet`, `ConfirmDialog`, `FormDialog` |
| Motion and waves | `MotionBox`, `Animated`, `StaggerGroup`, `WaveBackground`, `WaveLayer`, `WaveLoader`; the logo `WaveMark` (three rolling layers) |
| Other | `AnimatedTabs`, `AnimatedCount`, `UserAvatar` (standard avatar by gender), `StatusBadge`, `DataTable`, `PaginationControls`, `InfiniteGrid`, `LoadMore`, `SearchInput`, `SelectField`, `AppLink`, `PageHeader`, `PageContainer` |

Feature components live in `components/{chat,users,videos,layout}`; for example `OwnerBadge`, `CommentReplies`, `MessageBubble`, `ReactionPicker`, `VoiceNotePlayer`, `RequestBanner`, `GroupInfoSheet`.

## Config and constants

`config/theme/*` builds the Chakra system (brand gradient, semantic tokens for light and dark, keyframes including `waveDrift` and `waveSwell`). `config/api/endpoints/*` mirror the server routes. `constants/users/settings/setting-sections.constants.ts` drives the settings page. `constants/common/motion.constants.ts` holds animation variants.

## Tests

`npm test` (Vitest, jsdom, Testing Library): 11 tests in 5 files covering formatting, avatars, message grouping, Seen counts, the edit window, redirect safety, and the multi-file queue.
