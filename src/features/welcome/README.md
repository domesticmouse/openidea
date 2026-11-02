# welcome

## Overview
Welcome screen (Projects, Customize, Plugins, Learn), Recent Projects popup and title bar project switcher, and automatic last-session project reopening on startup.

## Interface
- [index.ts:activate] Registers session tracking, WelcomeView, ProjectPopup, RecentProjectsPopup overlay, and welcome.recentProjects / file.openRecent actions, then triggers reopenLastProject().
- [WelcomeView.tsx:WelcomePage] [WelcomeView.tsx:GEAR_MENU] [WelcomeView.tsx:WelcomeView] [ProjectsPage.tsx:handleListKey] [ProjectsPage.tsx:ProjectsPage] [CustomizePage.tsx:THEME_OPTIONS] [CustomizePage.tsx:UI_FONT_SIZES] [CustomizePage.tsx:setGlobalSetting] [CustomizePage.tsx:customizeView] [CustomizePage.tsx:CustomizePage] Welcome screen shell and Projects/Customize pages.
- [RecentList.tsx:askRemoveRecent] [RecentList.tsx:ProjectBadge] [RecentList.tsx:recentMenu] [RecentList.tsx:RecentListProps] [RecentList.tsx:RecentList] [RecentProjectsPopup.tsx:useRecentPopup] [RecentProjectsPopup.tsx:showRecentProjectsPopup] [RecentProjectsPopup.tsx:RecentProjectsPopup] [ProjectPopup.tsx:ProjectPopup] Shared recent-projects list component, modal popup, and title bar project switcher.
- [recent.ts:useRecent] [recent.ts:loadRecent] [recent.ts:remoteDisplay] [recent.ts:removeRecent] [recent.ts:openRecent] [recent.ts:tildify] [recent.ts:RecentMatch] [recent.ts:filterRecent] Recent projects store merging local and SSH remote projects, fuzzy name/path filtering, and project opening.
- [session.ts:useSession] [session.ts:setReopenPreference] [session.ts:reopenEnabled] [session.ts:trackSession] [session.ts:lastSessionProject] [session.ts:reopenLastProject] [session.ts:_resetSessionForTests] Session persistence and startup project restoration.

## Invariants
- [session.ts:trackSession] [session.ts:reopenLastProject] Explicit CLI/OS startup launches and per-window sessionStorage (openidea.window.project) take precedence over openidea.session.openProject, and explicit project close clears the remembered session project.
- [recent.ts:filterRecent] Ranks name fuzzy matches ahead of path fuzzy matches while preserving recency order within each group.

## Side Effects
- [recent.ts:loadRecent] [recent.ts:removeRecent] [session.ts:reopenLastProject] Calls IPC commands.recentProjects(), commands.appInfo(), commands.removeRecentProject(), commands.settingsGet(), and @tauri-apps/api/path homeDir().
- [session.ts:trackSession] [session.ts:setReopenPreference] Reads and writes localStorage (openidea.session.openProject, openidea.welcome.reopenLastProject) and sessionStorage (openidea.window.project).

## Verification
- Run: `pnpm test`
