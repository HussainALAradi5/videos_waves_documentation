# Mobile reference (`videos_waves_mobile`)

## Services (`lib/services`), behind interfaces in `lib/interfaces/services`

| Service | Functions → endpoint |
| --- | --- |
| `AuthService` | `login`, `register`, `me` (`GET /users/me`), `logout` |
| `VideoService` | `list`, `getById`, `upload` (multipart from a path or bytes, with progress), `setLiked` (`PUT`/`DELETE …/like`), `recordView` |
| `CommentService` | `list`, `create`, `remove` |
| `AdminRequestService` | `list`, `create`, `cancel`, `decide` |
| `TokenStorage` | `flutter_secure_storage` (Keychain / Keystore) |
| `ApiClient` | One Dio instance; unwraps the envelope; throws `ApiException` |

## Middlewares (`lib/middlewares`)

| Interceptor | Purpose |
| --- | --- |
| `AuthInterceptor` (`QueuedInterceptor`) | Bearer header; on 401, one refresh and a retry through a plain Dio (so it cannot loop or deadlock); on failure, clears tokens and calls `AuthController.handleSessionExpired` |
| `ErrorInterceptor` | Attaches an `ApiException` to every `DioException` |

## Controllers (`lib/controllers`)

| Controller | Purpose |
| --- | --- |
| `PagedListController<T>` | Shared paging: `loadFirstPage`, `loadNextPage`, a generation counter that drops stale results, `insertFirst`, `removeWhere`, `replaceWhere` |
| `VideoFeedController` | Feed with a debounced search; `updateVideo` syncs changes from the details screen |
| `VideoDetailsController` | Load, optimistic like with rollback, one view per visit, comment count |
| `CommentListController` | Paged comments, add, remove |
| `VideoUploadController` | Pick a file (`file_picker`), upload with progress |
| `AuthController` | `restore`, `login`, `register`, `logout`, session expiry; the router's `refreshListenable` |
| `MyAdminRequestController`, `AdminRequestListController` | A user's own request; the admin review list |

## Routing (`lib/config/routes`)

go_router with a `ShellRoute` (bottom navigation: Feed, Upload, Profile). `resolveRedirect` sends guests away from `/upload`, `/profile`, and `/admin/requests` to `/login?redirect=…`, and sends non-admins away from `/admin/requests`. `safeRedirect` accepts only in-app paths.

## Reusable widgets (`lib/widgets/common`)

`PagedListView<T>`, `LoadingView`, `EmptyView`, `ErrorView`, `AppTextField`, `SubmitButton`, `ConfirmDialog`, `StatusChip`, `UserAvatar`, `ConstrainedContent`.
