# Web client reference (`videos_waves_client`)

## Services (`src/services`), all through one Axios `apiClient`

| Service | Functions → endpoint |
| --- | --- |
| `authService` | `register` → `POST /auth/register`, `login` → `POST /auth/login`, `logout` → `POST /auth/logout` |
| `userService` | `getMe` → `GET /users/me`, `updateMe` → `PATCH /users/me`, `changePassword` → `PATCH /users/me/password`, `deactivate` → `DELETE /users/me` |
| `videoService` | `list`, `getById`, `upload` (multipart with progress), `update`, `remove`, `recordView`, `like` (`PUT`), `unlike` (`DELETE`) |
| `commentService` | `list`, `create`, `update`, `remove` |
| `adminRequestService` | `list`, `create`, `decide`, `cancel` |
| `tokenStorage` | Guarded `localStorage` access plus a change event for `useSyncExternalStore` |

`api-request.ts` unwraps the envelope (`getData`, `getPage`, `postData`, `putData`, `patchData`, `deleteData`). Every failure becomes an `ApiError { status, code, message, details, requestId }`.

## Middlewares (`src/middlewares`)

| File | Purpose |
| --- | --- |
| `auth/auth.interceptor.ts` | Bearer header; on 401, one shared refresh (`pendingRefresh`), then a retry; on failure, clears tokens and emits `session-expired` |
| `errors/error.interceptor.ts` | Maps every error to `ApiError` |
| `auth/require-auth.tsx` | Route guard component (optional `role`) |

## Hooks (`src/hooks`): all state and data logic

| Group | Hooks |
| --- | --- |
| `users/auth` | `useAuthSession` (session provider value), `useAuth`, `useLogin`, `useRegister`, `useLogout`, `useRequireAuth` |
| `users/user` | `useProfileActions`, `useProfileDialogs` |
| `users/admin-request` | `useAdminRequests` (paged with status filter), `useAdminRequestActions` |
| `videos/video` | `useVideoFeed` (infinite), `useFeedFilters` (debounced search, sort), `useVideo`, `useVideoDetails`, `useVideoLike` (optimistic with rollback), `useRecordView`, `useVideoOwnerActions`, `useUploadVideo` |
| `videos/comment` | `useComments` (infinite), `useCommentActions`, `useCommentItem`, `useCommentComposer` |
| `common` | `useGenericForm`, `useInViewTrigger`, `useDebouncedValue`, `useFileDropzone`, `useColorMode` |

`utilities/cache/video-cache.utility.ts` → `patchVideoInCache` updates a video in every cached feed and detail entry, so likes, views, and comment counts change in place without a refetch.

## Generic components (`src/components/common`)

| Component | Props (in `src/interfaces/components/common`) | Used by |
| --- | --- | --- |
| `GenericForm<T>` | `schema`, `fields: FormFieldConfig[]`, `defaultValues`, `submitLabel`, `onSubmit`, `isSubmitting`, `children`, `secondaryAction` | Login, register, upload, admin request |
| `FormDialog<T>` | `GenericFormProps` + `isOpen`, `title`, `onClose` | Edit profile, change password, edit video |
| `ConfirmDialog` | `title`, `description`, `confirmLabel`, `onConfirm`, `isLoading`, `colorPalette` | Delete video and comment, deactivate |
| `InfiniteGrid<T>` | `items`, `getKey`, `renderItem`, paging props | Feed, my videos |
| `DataTable<T>` | `columns: TableColumn<T>[]`, `rows`, `getKey` | Admin requests |
| `PaginationControls` | `meta`, `onPageChange` | Admin requests |
| `QueryState` | `isLoading`, `error`, `isEmpty`, `empty`, `onRetry` | Every query-backed section |
| `StatusBadge<T>` | `value`, `options: Record<T, StatusOption>` | Roles, request statuses |
| `FileDropzone` | `file`, `onFileChange`, `accept`, `maxSizeBytes`, labels | Upload |
| `SearchInput`, `SelectField<T>`, `UserAvatar`, `AppLink`, `PageHeader`, `PageContainer`, `LoadingState`, `EmptyState`, `ErrorState` | — | Shared |

## Config and constants

`config/api/endpoints/*.endpoints.ts` mirror the server routes. `constants/common/messages.constants.ts` holds every UI string. `constants/**/**-form-fields.constants.ts` hold the form field configs, and `constants/users/**/…` the enum label and color maps.
