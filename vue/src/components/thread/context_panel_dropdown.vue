<script lang="coffee">
import AbilityService from '@/shared/services/ability_service'
import ThreadService from '@/shared/services/thread_service'
import Session from '@/shared/services/session'
import DiscussionModalMixin from '@/mixins/discussion_modal'
import ConfirmModalMixin from '@/mixins/confirm_modal'
import ChangeVolumeModalMixin from '@/mixins/change_volume_modal'
import LmoUrlService from '@/shared/services/lmo_url_service'

export default
  mixins: [ DiscussionModalMixin, ConfirmModalMixin, ChangeVolumeModalMixin ]
  props:
    discussion: Object
  # data: ->
  methods:
    canChangeVolume: ->
      AbilityService.canChangeVolume(@discussion)

    openChangeVolumeForm: ->
      @openChangeVolumeModal(@discussion)

    canEditThread: ->
      AbilityService.canEditThread(@discussion)

    canMuteThread: ->
      Session.isSignedIn()

    editThread: ->
      # ModalService.open 'DiscussionEditModal', discussion: => @discussion
      @openEditDiscussionModal(@discussion)

    canPinThread: ->
      AbilityService.canPinThread(@discussion)

    closeThread: ->
      ThreadService.close(@discussion)

    reopenThread: ->
      ThreadService.reopen(@discussion)

    pinThread: ->
      ThreadService.pin(@discussion)

    unpinThread: ->
      ThreadService.unpin(@discussion)

    muteThread: ->
      ThreadService.mute(@discussion)

    unmuteThread: ->
      ThreadService.unmute(@discussion)

    canMoveThread: ->
      AbilityService.canMoveThread(@discussion)

    canCloseThread: ->
      AbilityService.canCloseThread(@discussion)

    moveThread: ->
      ModalService.open 'MoveThreadForm', discussion: => @discussion

    requestPagePrinted: ->
      EventBus.broadcast $rootScope, 'toggleSidebar', false
      EventBus.broadcast $rootScope, 'fetchRecordsForPrint'

    canDeleteThread: ->
      AbilityService.canDeleteThread(@discussion)

    deleteThread: ->
      @openConfirmModal(
        submit:     @discussion.destroy
        text:
          title:    'delete_thread_form.title'
          helptext: 'delete_thread_form.body'
          submit:   'delete_thread_form.confirm'
          flash:    'delete_thread_form.messages.success'
        redirect:   LmoUrlService.group @discussion.group()
      )
</script>
<template lang="pug">
.context-panel-dropdown.pull-right.lmo-no-print
  //- outlet.context-panel__before-thread-actions(name='before-thread-actions', model='discussion')
  //- md-menu.lmo-dropdown-menu(md-position-mode='target-right target')
  //-   md-button.context-panel-dropdown__button(ng-click='$mdMenu.open()')
  //-     .sr-only(translate='thread_context.thread_options')
  //-     i.mdi.mdi-24px.mdi-chevron-down
  v-menu.lmo-dropdown-menu(offset-y)
    v-btn.context-panel-dropdown__button(flat slot='activator')
      span(v-t="'thread_context.thread_options'")
      v-icon mdi-chevron-down

    v-list
      v-list-tile.context-panel-dropdown__option--email-settings(v-if='canChangeVolume()' @click='openChangeVolumeForm()')
        v-list-tile-title(v-t="'thread_context.email_settings'")
      v-list-tile.context-panel-dropdown__option--edit(v-if='canEditThread()' @click='editThread()')
        v-list-tile-title(v-t="'thread_context.edit'")
      v-list-tile.context-panel-dropdown__option--pin(ng-show='!discussion.pinned && canPinThread()' @click='pinThread()')
        v-list-tile-title(v-t="'thread_context.pin_thread'")
      v-list-tile.context-panel-dropdown__option--pin(ng-show='discussion.pinned && canPinThread()' @click='unpinThread()')
        v-list-tile-title(v-t="'thread_context.unpin_thread'")
      v-list-tile.context-panel-dropdown__option--close(ng-show='!discussion.closedAt && canCloseThread()' @click='closeThread()')
        v-list-tile-title(v-t="'thread_context.close_thread'")
      v-list-tile.context-panel-dropdown__option--reopen(ng-show='discussion.closedAt && canCloseThread()' @click='reopenThread()')
        v-list-tile-title(v-t="'thread_context.reopen_thread'")
      v-list-tile.context-panel-dropdown__option--mute(v-if='canMuteThread() && !discussion.isMuted()' @click='muteThread()')
        v-list-tile-title(v-t="'volume_levels.mute'")
      v-list-tile.context-panel-dropdown__option--unmute(ng-show='discussion.isMuted()' @click='unmuteThread()')
        v-list-tile-title(v-t="'volume_levels.unmute'")
      v-list-tile.context-panel-dropdown__option--move(v-if='canMoveThread()' @click='moveThread()')
        v-list-tile-title(v-t="'thread_context.move_thread'")
      v-list-tile.context-panel-dropdown__option--print(@click='requestPagePrinted()')
        v-list-tile-title(v-t="'thread_context.print_thread'")
      v-list-tile.context-panel-dropdown__option--delete(v-if='canDeleteThread()' @click='deleteThread()')
        v-list-tile-title(v-t="'thread_context.delete_thread'")
</template>
<style lang="scss">
</style>




  ]
