# Mobile reference (`videos_waves_mobile`)

## Services (`lib/services`)

| Service | Functions → endpoint |
| --- | --- |
| `ApiClient` | One Dio instance; `get`, `getList`, `getPage`, `getCursorPage`, `post`, `put`, `patch`, `delete`; unwraps the envelope; throws `ApiException` |
| `AuthService` | `login`, `register` (with gender), `me`, `logout` (this device) |
| `VideoService` | `list` (scope, owner, search), `getById`, `create` (`mediaId`), `update`, `remove`, `setLiked`, `recordView` |
| `CommentService` | `list`, `replies`, `create` (`parentId`), `update`, `remove`, `restore`, `approve` |
| `MediaUploadService` | `upload(file, kind, onProgress)`: signature → multipart upload to the provider → register |
| `SocialService` | `profile`, `search`, `followers`, `following`, `setFollowing`, `setBlocked`, `setRestricted` |
| `SettingsService` | `get`, `update`, `updateAvatar`, `updateProfile` |
| `ConversationService` | `list(folder)`, `get`, `unreadCount`, `startDirect`, `createGroup`, `accept`, `hide`, `markRead`, `setMuted`, `leave` |
| `MessageService` | `list` (cursor), `send`, `edit`, `remove(scope)`, `react`, `unreact` |
| `SocketService` | One Socket.IO connection; listeners survive reconnects; sends the stored access token on each reconnect |
| `AdminRequestService`, `TokenStorage` | Unchanged (`flutter_secure_storage`) |

## Middlewares (`lib/middlewares`)

| Class | Purpose |
| --- | --- |
| `AuthInterceptor` | Bearer header; on 401 one refresh through `TokenRefresher`, then a retry |
| `TokenRefresher` | Returns `RefreshOutcome.refreshed`, `rejected` (401: tokens cleared, session expired), or `offline` (tokens kept) |
| `ErrorInterceptor` | Attaches an `ApiException` to every `DioException` |

## Controllers (`lib/controllers`)

| Controller | Purpose |
| --- | --- |
| `PagedListController<T>` | Shared paging with a generation counter; `insertFirst`, `insertAt` (Undo), `indexWhere`, `removeWhere`, `replaceWhere` |
| `AuthController` | `restore` (clears tokens only on 401), `login`, `register`, `logout`, `updateUser`, session expiry |
| `SettingsController` | Loads settings on sign-in; optimistic `change` with rollback; `themeMode` for the app |
| `AccountController` | `changeAvatar` (upload with progress), `removeAvatar`, `updateBio` |
| `ProfileController` | Profile with optimistic follow and restrict; block reloads the profile |
| `UserListController` | Search, followers, following with debounced search and inline Follow |
| `VideoFeedController` | For you / Following, search, or one owner's videos |
| `VideoDetailsController` | Load, optimistic like, one view per visit, owner `update` and `remove` |
| `CommentListController` | Comments or one comment's replies: `add`, `edit`, `remove` (returns the index), `restore`, `approve`, `adjustReplies` |
| `UploadQueueController` | Generic queue: each file uploads at once with progress, remove, retry |
| `VideoBatchUploadController` | Up to 10 videos: titles from file names, then `publish` in order |
| `VoiceRecorderController` | `record` (webm on the web, m4a on devices), 5-minute limit |
| `ChatSocketController` | Connects the socket while signed in; Messages badge |
| `InboxController` | Folders; reloads quietly on live events |
| `ThreadController` (+ `ThreadSocketBinding`, `TypingTracker`, `ThreadMessageActions`) | Cursor-paged messages, live events, typing, send, edit, unsend, delete for me, react, accept |

## Routing (`lib/config/routes`)

go_router with a `ShellRoute` of five tabs: Home `/`, Search `/search`, Upload `/upload`, Messages `/messages` (unread badge), Profile `/profile`. Outside the shell: `/u/:userName`, `/messages/:conversationId`, `/videos/:videoId`, `/settings`, `/admin/requests`, `/login`, `/register`. `resolveRedirect` sends guests away from upload, messages, profile, settings, and admin routes to `/login?redirect=…`; `safeRedirect` accepts only in-app paths.

## Widgets (`lib/widgets`)

| Group | Widgets |
| --- | --- |
| Buttons and menus | `AppButton` (gradient, primary, secondary, outline, ghost, danger; spinner and no repeat taps), `ActionSheetButton` (⋯ sheet, destructive last) |
| Dropdowns | `AppDropdown` (animated custom menu), `MultiSelectSheet` (search and chips), `SegmentedTabs` |
| Uploads | `MultiFilePicker`, `FilePreviewTile` |
| Feedback | `AppProgressBar`, `SkeletonList`, `LoadingView` (wave loader), `EmptyView`, `ErrorView`, `showUndoSnackBar` |
| Motion and waves | `FadeSlideIn` (respects reduced motion), `WaveBackground`, `WaveLoader`, `WaveMark` (the rolling logo), `BrandTitle` |
| Users | `UserAvatar` (photo or the standard male, female, or neutral SVG; online dot), `FollowButton`, `UserTile`, `UserListView`, `UserListSheet`, `ProfileCount` |
| Videos | `VideoCard` (thumbnail, duration, Hero), `CommentTile`, `OwnerBadge`, `CommentComposer`, `InlineEditor` |
| Chat | `ConversationTile`, `MessageBubble` (bubble colour from settings), `MessageAttachments`, `VoiceNotePlayer`, `TypingDots`, `SystemNotice`, `AttachmentStrip`, `RecordingBar`, `ReplyPreview`, `SendButton` |

## Config

`ApiConfig.baseUrl` comes from `--dart-define=API_BASE_URL=…` (default `http://10.0.2.2:5000` for the Android emulator; use `http://localhost:5000` for a web build). `AppTheme` provides Material 3 light and dark themes in the brand colours; the active one follows the `theme` setting.

## Tests

`flutter test`: 24 tests covering the route guard, feed paging, video details, models, formatters, validations, `PagedListView`, comment delete and Undo, multi-video upload, Seen counts, and `AppButton`'s busy state.
