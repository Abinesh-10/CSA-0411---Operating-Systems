#include <windows.h>
#include <stdio.h>
#include <string.h>

int main() {
    HANDLE hMapFile;
    char *pBuf;

    hMapFile = CreateFileMapping(INVALID_HANDLE_VALUE, NULL,
                                 PAGE_READWRITE, 0, 1024, "MyMessageQueue");
    if (hMapFile == NULL) {
        printf("Could not create file mapping (%lu).\n", GetLastError());
        return 1;
    }

    pBuf = (char *)MapViewOfFile(hMapFile, FILE_MAP_ALL_ACCESS, 0, 0, 1024);
    if (pBuf == NULL) {
        printf("Could not map view of file (%lu).\n", GetLastError());
        CloseHandle(hMapFile);
        return 1;
    }

    strcpy(pBuf, "Hello from Windows shared memory!");
    printf("Message written: %s\n", pBuf);

    UnmapViewOfFile(pBuf);
    CloseHandle(hMapFile);
    return 0;
}
