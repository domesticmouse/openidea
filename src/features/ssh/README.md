# ssh

## Overview
SSH credential prompt queue (askpass) and modal dialog for ssh/git child processes, plus Tools › Start SSH Session… chooser and terminal session launcher.

## Interface
- [index.ts:activate] Subscribes to askpass/askpassDone events and registers ssh.askpass overlay and ssh.startSession/ssh.configurations actions.
- [askpass.ts:AskpassPrompt] [askpass.ts:useAskpass] [askpass.ts:enqueueAskpass] [askpass.ts:dropAskpass] [askpass.ts:askpassAnswer] [askpass.ts:answerAskpass] FIFO prompt queue andIPC answer dispatcher.
- [AskpassDialog.tsx:AskpassHost] Modal dialog for SSH password, passphrase, and host-key confirmation prompts.
- [session.ts:SSH_SETTINGS_PAGE] [session.ts:sessionItems] [session.ts:startSshSession] [session.ts:chooseSshSession] SSH configuration picker and terminal command launcher.

## Invariants
- [askpass.ts:enqueueAskpass] [AskpassDialog.tsx:AskpassHost] Deduplicates queued prompts by id and displays strictly one prompt (queue[0]) at a time in FIFO order.
- [askpass.ts:askpassAnswer] Maps "confirm" prompts exclusively to "yes"/"no" (or null on cancel), and non-confirm prompts to text.

## Side Effects
- [index.ts:activate] Subscribes to askpass and askpassDone IDE events, registers ssh.askpass overlay, and registers ssh.startSession and ssh.configurations actions.
- [askpass.ts:answerAskpass] [session.ts:startSshSession] Calls commands.sshAskpassAnswer, commands.sshTerminalCommand, and executes terminal.runCommand.

## Verification
- Run: `pnpm test`
