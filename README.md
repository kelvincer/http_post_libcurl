# Code
This is how to make a POST request using libcurl library using the C programming language. I am using [TMDB](https://developer.themoviedb.org/reference/intro/getting-started) api to make this demo.
```c
#include <curl/curl.h>

int main()
{
    const char *jsonString = "{\"value\":8.5}";

    curl_global_init(CURL_GLOBAL_ALL);
    CURL *curl = curl_easy_init();

    int movieId = 142064;
    char fullURL[200];
    char *URL = "https://api.themoviedb.org/3/movie/%d/rating";

    sprintf(fullURL, URL, movieId);

    if (curl)
    {
        curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, "POST");
        curl_easy_setopt(curl, CURLOPT_WRITEDATA, stdout);
        curl_easy_setopt(curl, CURLOPT_URL, fullURL);

        struct curl_slist *headers = NULL;
        headers = curl_slist_append(headers, "accept: application/json");
        headers = curl_slist_append(headers, "Content-Type: application/json;charset=utf-8");
        headers = curl_slist_append(headers, "Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiI2NDM1MTgzZmM4ZTgyOTQ3OGNiNWY4NTM5ZWZkMWU3MSIsIm5iZiI6MTcyNzAyODk1My45NTM4NzUsInN1YiI6IjVjNjA0NjE1OTI1MTQxMzM3NmMwMWQ3OCIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.ojN56CsCIKIhTfNlXob_o48Npl3xBiznLJls-jrNMRY");

        curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
        curl_easy_setopt(curl, CURLOPT_POSTFIELDS, jsonString);

        CURLcode ret = curl_easy_perform(curl);

        curl_slist_free_all(headers);
        curl_easy_cleanup(curl);
    }

    curl_global_cleanup();

    return 0;
}
```
# Console output
Here is the output of the previous code, running on macOS.
```console
MacBook-Pro-de-kelvin:libcurl kelvinc$ cc post.c -o o -lcurl
MacBook-Pro-de-kelvin:libcurl kelvinc$ ./o
{"success":true,"status_code":12,"status_message":"The item/record was updated successfully."}
```
This is the output using leaks tool in macOS to check memory leaks.
```console
Pro-de-kelvin:libcurl kelvinc$ leaks --atExit --list  -- ./o
o(11113) MallocStackLogging: could not tag MSL-related memory as no_footprint, so those pages will be included in process footprint - (null)
o(11113) MallocStackLogging: recording malloc (and VM allocation) stacks using lite mode
{"success":true,"status_code":12,"status_message":"The item/record was updated successfully."}
Process:         o [11113]
Path:            /Users/USER/*/o
Load Address:    0x1051ce000
Identifier:      o
Version:         0
Code Type:       X86-64
Platform:        macOS
Parent Process:  leaks [11112]

Date/Time:       2024-09-29 14:30:21.625 -0500
Launch Time:     2024-09-29 14:30:20.203 -0500
OS Version:      macOS 15.0 (24A335)
Report Version:  7
Analysis Tool:   /Applications/Xcode.app/Contents/Developer/usr/bin/leaks
Analysis Tool Version:  Xcode 16.0 (16A242d)

Physical footprint:         3760K
Physical footprint (peak):  3760K
Idle exit:                  untracked
----

leaks Report Version: 3.0
Process 11113: 3262 nodes malloced for 229 KB
Process 11113: 0 leaks for 0 total leaked bytes.
``` 
