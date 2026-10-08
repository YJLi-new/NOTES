# CS340 mp1

### `dll.c`

```c
#include "dll.h"
#include <string.h>
#include <stdlib.h>

// node
void dllDetachc(dllNodec *self) {
    if (self->prev) { self->prev->next = self->next; }
    if (self->next) { self->next->prev = self->prev; }
}

void dllDetachs(dllNodes *self) {
    if (self->prev) { self->prev->next = self->next; }
    if (self->next) { self->next->prev = self->prev; }
}

// list
void dllInitc(dllListc *self) {
    self->head = self->tail = NULL;
}
void dllInits(dllLists *self) {
    self->head = self->tail = NULL;
}

dllNodec *dllFindc(const dllListc *self, const char value) {
    dllNodec *cursor = self->head;
    while (cursor) {
        if (cursor->data == value) { return cursor; }
        cursor = cursor->next;
    }
    return NULL;
}
dllNodes *dllFinds(const dllLists *self, const char *value) {
    dllNodes *cursor = self->head;
    while (cursor) {
        if (strcmp(cursor->data, value) == 0) { return cursor; }  // !
        cursor = cursor->next;
    }
    return NULL;
}

void dllClearc(dllListc *self) {
    while(self->head) { dllPopc(self, '\0'); }
}
void dllClears(dllLists *self) {
    while(self->head) { dllPops(self, NULL); }
}

char dllPopc(dllListc *self, char ifEmpty) {
    if (!self->head) { return ifEmpty; }
    dllNodec *tmp = self->head;
    self->head = tmp->next;
    if (!tmp->next) { self->head = self->tail = NULL; }
    dllDetachc(tmp);
    char ans = tmp->data;
    free(tmp); 
    return ans;
}

char *dllPops(dllLists *self, char *ifEmpty) {
    if (!self->head) { return ifEmpty; }
    dllNodes *tmp = self->head;
    self->head = tmp->next;
    if (!tmp->next) { self->head = self->tail = NULL; }
    dllDetachs(tmp);
    char *ans = (char *)malloc(strlen(tmp->data) + 1);
    strcpy(ans, tmp->data);
    free(tmp); 
    return ans;
}

char dllShiftc(dllListc *self, char ifEmpty) {
    if (!self->tail) return ifEmpty;
    dllNodec *tmp = self->tail;
    if (!tmp->prev) self->head = self->tail = NULL;
    self->tail = tmp->prev;
    dllDetachc(tmp);
    char ans = tmp->data;
    free(tmp); 
    return ans;
}

char *dllShifts(dllLists *self, char *ifEmpty) {
    if (!self->tail) return ifEmpty;
    dllNodes *tmp = self->tail;
    if (!tmp->prev) self->head = self->tail = NULL;
    self->tail = tmp->prev;
    dllDetachs(tmp);
    char *ans = (char *)malloc(strlen(tmp->data) + 1);
    strcpy(ans, tmp->data);
    free(tmp); 
    return ans;
}

void dllPushc(dllListc *self, char value) {
    dllNodec *tmp = (dllNodec *) malloc(sizeof(dllNodec));
    tmp->data = value;
    tmp->next = self->head;
    tmp->prev = NULL;
    if (self->head) { self->head->prev = tmp; }
    self->head = tmp;
    if (!self->tail) { self->tail = self->head; }
}
void dllPushs(dllLists *self, char *value) {
    dllNodes *tmp = (dllNodes *) malloc(sizeof(dllNodes));
    tmp->data = (char *) malloc(strlen(value) + 1);
    strcpy(tmp->data, value);
    tmp->next = self->head;
    tmp->prev = NULL;
    if (self->head) { self->head->prev = tmp; }
    self->head = tmp;
    if (!self->tail) { self->tail = self->head; }
}

void dllEnqueuec(dllListc *self, char value) {
    dllNodec *tmp = (dllNodec *) malloc(sizeof(dllNodec));
    tmp->data = value;
    tmp->prev = self->tail;
    tmp->next = NULL;
    if (self->tail) { self->tail->next = tmp; }
    self->tail = tmp;
    if (!self->head) { self->head = self->tail; }
}
void dllEnqueues(dllLists *self, char *value) {
    dllNodes *tmp = (dllNodes *) malloc(sizeof(dllNodes));
    tmp->data = (char *) malloc(strlen(value) + 1);
    strcpy(tmp->data, value);
    tmp->prev = self->tail;
    tmp->next = NULL;
    if (self->tail) { self->tail->next = tmp; }
    self->tail = tmp;
    if (!self->head) { self->head = self->tail; }
}
```

### `dll.h`

```c
#ifndef _LINKED_LIST_H_
#define _LINKED_LIST_H_

struct dllNodec;
struct dllNodes;
struct dllListc;
struct dllLists;

typedef struct dllNodec {
  char data;
  struct dllNodec *next;
  struct dllNodec *prev;
} dllNodec;

typedef struct dllNodes {
  char *data;
  struct dllNodes *next;
  struct dllNodes *prev;
} dllNodes;

void dllDetachc(dllNodec *self);
void dllDetachs(dllNodes *self);


typedef struct dllListc {
  dllNodec *head, *tail;
} dllListc;

typedef struct dllLists {
  dllNodes *head, *tail;
} dllLists;


void dllInitc(dllListc *self);
void dllInits(dllLists *self);

dllNodec *dllFindc(const dllListc *self, const char value);
dllNodes *dllFinds(const dllLists *self, const char *value);

void dllClearc(dllListc *self);
void dllClears(dllLists *self);

char dllPopc(dllListc *self, char ifEmpty);
char *dllPops(dllLists *self, char *ifEmpty);

char dllShiftc(dllListc *self, char ifEmpty);
char *dllShifts(dllLists *self, char *ifEmpty);

void dllPushc(dllListc *self, char value);
void dllPushs(dllLists *self, char *value);

void dllEnqueuec(dllListc *self, char value);
void dllEnqueues(dllLists *self, char *value);

#endif
```
