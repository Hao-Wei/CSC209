#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
#include "lists.h"

/*
 * Return a pointer to the struct calendar with name cal_name
 * or NULL if no calendar with this name exists in the cal_list
 */
Calendar *find_calendar(Calendar *cal_list, char *cal_name) {
	int length = strlen(cal_name);
	while(cal_list != NULL)
	{
		if(strncmp(cal_list->name, cal_name, length+1) == 0)
			return cal_list;
		cal_list = cal_list->next;
	}
    return NULL;
}


/* 
 * Return a pointer to the user with name username or NULL
 *   if no such user exists in user_list 
 */
User *find_user(User *user_list, char *username) {
    int length = strlen(username);
	while(user_list != NULL)
	{
		if(strncmp(user_list->name, username, length+1) == 0)
			return user_list;
		user_list = user_list->next;
	}
    return NULL;
}


/* 
 * If a calendar with name cal_name does not exist, create a new
 * calendar by this name and insert it at the front of the calendar list.
 * Return values:
 *    0 if successful
 *   -1 if a calendar by cal_name already exists
 */
int add_calendar(Calendar **cal_list_ptr, char *cal_name) {
	if(cal_list_ptr == NULL || find_calendar(*cal_list_ptr, cal_name) != NULL)
		return -1;
	Calendar *new_cal;
	if((new_cal = malloc(sizeof(Calendar))) == NULL) {
		perror("malloc");
		exit(1);
	}
	int length = strlen(cal_name);
	if((new_cal->name = malloc(length+1)) == NULL) {
		perror("malloc");
		exit(1);
	}
	strncpy(new_cal->name, cal_name, length+1);
	new_cal->events = NULL;
	new_cal->next = *cal_list_ptr;
    *cal_list_ptr = new_cal;
    return 0;
}

/* 
 * Print to stdout a list of all current calendar names (one per line)
 */
void list_calendars(Calendar *cal_list) {
	while(cal_list != NULL)
	{
		printf("%s\n", cal_list->name);	
		cal_list = cal_list->next;
	}
}


/* 
 * If a user with name username does not exist, create a new
 * user by this name and insert it at the end of the user list.
 * Return values:
 *    0 if successful
 *   -1 if username already exists
 */
int add_user(User **user_list_ptr, char *username) {
	if(user_list_ptr == NULL || find_user(*user_list_ptr, username) != NULL)
		return -1;
	User *new_user;
	if((new_user = malloc(sizeof(User))) == NULL) {
		perror("malloc");
		exit(1);
	}
	int length = strlen(username);
	if((new_user->name = malloc(length+1)) == NULL) {
		perror("malloc");
		exit(1);
	}
	strncpy(new_user->name, username, length+1);
	new_user->subscriptions = NULL;
	new_user->next = NULL;
	if(*user_list_ptr == NULL)
		*user_list_ptr = new_user;
	else
	{
		User* user = *user_list_ptr;
		while(user->next != NULL)
			user = user->next;
		user->next = new_user;			
	}
    return 0;
}


/* 
 * Print to stdout a list of usernames one per line 
 */
void list_users(User *user_list) {
	while(user_list != NULL)
	{
		printf("%s\n", user_list->name);	
		user_list = user_list->next;
	}
}


/*
 * Subscribe the user username to calendar cal_name
 * Return:
 *    0 if successful
 *   -1 if no user exists by this name
 *   -2 if no calendar exists by this name
 *   -3 if this user is already subscribed to this calendar
 */
int subscribe(User *user_list, Calendar *cal_list, char *username, char *cal_name) {
	User* user;
	Calendar* cal;
	if(user_list == NULL || (user = find_user(user_list, username)) == NULL)
		return -1;
	if(cal_list == NULL || (cal = find_calendar(cal_list, cal_name)) == NULL)
		return -2;
	Subscription* sub = user->subscriptions;
	int length = strlen(cal_name);
	if(sub == NULL)
	{
		if((user->subscriptions = malloc(sizeof(Subscription))) == NULL) {
			perror("malloc");
			exit(1);
		}
		user->subscriptions->calendar = cal;
		user->subscriptions->next = NULL;
	}
	else
	{
		while(sub->next != NULL)
		{
			if(strncmp(sub->calendar->name, cal_name, length+1) == 0)
				return -3;
			sub = sub->next;
		}
		if(strncmp(sub->calendar->name, cal_name, length+1) == 0)
			return -3;
		Subscription* new_sub = user->subscriptions;
		if((new_sub = malloc(sizeof(Subscription))) == NULL) {
			perror("malloc");
			exit(1);
		}
		new_sub->calendar = cal;
		new_sub->next = NULL;
		sub->next = new_sub;
	}
	return 0;
}


/* 
 * Add an event with this name and date to the calender with name cal_name 
 *  Return:
 *   0 for success
 *  -1 for calendar does not exist by this name
 */
int add_event(Calendar *cal_list, char *cal_name, char *event_name, time_t time) {
	Calendar* cal;
	if(cal_list == NULL || (cal = find_calendar(cal_list, cal_name)) == NULL)
		return -1;
	int length = strlen(event_name);
	Event* new_event;
	if((new_event = malloc(sizeof(Event))) == NULL) {
		perror("malloc");
		exit(1);
	}

	if((new_event->description = malloc(length+1)) == NULL) {
		perror("malloc");
		exit(1);
	}
		
	strncpy(new_event->description, event_name, length+1);
	new_event->time = time;
	
	if(cal->events == NULL)
	{
		new_event->next = NULL;
		cal->events = new_event;
	}
	else
	{
		if(time <= cal->events->time)
		{
			new_event->next = cal->events;
			cal->events = new_event;
		}
		else
		{
			Event* e = cal->events;
			while(e->next != NULL && time > e->next->time)
				e = e->next;
			new_event->next = e->next;
			e->next = new_event;
		}
	}
    return 0;
}


/* 
 * Print to stdout a list of the events in this calendar ordered by time
 *  Return:
 *     0 if successful
 *    -1 if no calendar exists by this name
 */
int list_events(Calendar *cal_list, char *cal_name) {
	Calendar* cal;
	if(cal_list == NULL || (cal = find_calendar(cal_list, cal_name)) == NULL)
		return -1;
	Event* event_list = cal->events;
	while(event_list != NULL)
	{
		struct tm * timeinfo;
		timeinfo = localtime (&(event_list->time));
		char buffer [80];
		strftime (buffer,80,"%c",timeinfo);
		printf("%s: %s\n", event_list->description, buffer);	
		event_list = event_list->next;
	}
    return 0;
}


/* 
 * Print to stdout, the description and time of the next event for 
 * user user_name
 *  Return:
 *   0 on success
 *   -1 if this user_name does not exist or has no events
 */
int print_next_event(Calendar *cal_list, User * user_list, char *user_name) {
	User* user;
	if(user_list == NULL || (user = find_user(user_list, user_name)) == NULL)
		return -1;
	Event* next_event = NULL;
	Event* e;
	Subscription* s = user->subscriptions;
	Calendar* c;
	while(s != NULL)
	{
		c = s->calendar;
		e = c->events;
		while(e != NULL)
		{
			if(next_event == NULL || next_event->time > e->time)
				next_event = e;
			e = e->next;
		}
		s = s->next;
	}
	
	if(next_event == NULL)
		return -1;
	
	struct tm * timeinfo;
	timeinfo = localtime (&(next_event->time));
	char buffer [80];
	strftime (buffer,80,"%c",timeinfo);
	printf("%s: %s\n", next_event->description, buffer);
	
    return 0; 
}


/* 
 * Print to stdout a time-ordered list of all events in any calendars
 *  subscribed to by this user 
 *
 *  Do not change the calendar data-structure. 
 *  Return -1 if this user doesn't exist.
 */
int print_all_events(Calendar *cal_list, User * user_list, char *user_name) {
	User* user;
	if(user_list == NULL || (user = find_user(user_list, user_name)) == NULL)
		return -1;
	Event* event_list = NULL;
	Event* e, *ev;
	Subscription* s = user->subscriptions;
	Calendar* c;
	while(s != NULL)
	{
		c = s->calendar;
		e = c->events;
		while(e != NULL)
		{
			int length = strlen(e->description);
			Event* new_event;
			if((new_event = malloc(sizeof(Event))) == NULL) {
				perror("malloc");
				exit(1);
			}
			
			if((new_event->description = malloc(length+1)) == NULL) {
				perror("malloc");
				exit(1);
			}
		
			strncpy(new_event->description, e->description, length+1);
			new_event->time = e->time;
			
			if(event_list == NULL)
			{
				new_event->next = NULL;
				event_list = new_event;
			}
			else
			{
				ev = event_list;
				if(e->time <= ev->time)
				{
					new_event->next = ev;
					event_list = new_event;
				}
				else
				{
					while(ev->next != NULL && e->time > ev->next->time)
						ev = ev->next;
					new_event->next = ev->next;
					ev->next = new_event;
				}
			}
			e = e->next;
		}
		s = s->next;
	}
	
	while(event_list != NULL)
	{
		struct tm * timeinfo;
		timeinfo = localtime (&(event_list->time));
		char buffer [80];
		strftime (buffer,80,"%c",timeinfo);
		printf("%s: %s\n", event_list->description, buffer);
		e = event_list->next;
		free(event_list);
		event_list = e;
	}
	
    return 0;
}
