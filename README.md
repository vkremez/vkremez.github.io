# VK Simple Debugger
C++ reusuables:

1. Create_Process function
```
void create_process(char* path_to_exe)
	{
		STARTUPINFO si;
		PROCESS_INFORMATION pi;
		ZeroMemory(&si, sizeof(si));
		ZeroMemory(&pi, sizeof(pi));
		si.dwFlags = 0x1;
		si.wShowWindow = 0x0;
		si.cb = sizeof(si);
		if (!CreateProcess(path_to_exe,
			NULL,
			NULL,
			NULL,
			NULL,
			DEBUG_PROCESS, //CREATE_SUSPENDED
			NULL,
			NULL,
			&si,
			&pi)
			)
		{
			printf("[*] CreateProcess failed (%d).\n", GetLastError());
			return;
		}
		printf("[*] We have successfully launched with the process ID: %d\n", pi.dwProcessId);
		int xPID = pi.dwProcessId;
		HANDLE dwProcess = open_process(xPID);
		bool debugger_active = true;
		//printf("[*] We are in the debug mode: %s\n", debugger_active);
		return;
	}
	
 ``` 
2. Open Process function
```
HANDLE open_process(int PID)
	{
		printf("[*] Enter open_process() function...\n");
		dwProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, PID);
		return dwProcess;
	}
```
3.
<!-- HTML generated using hilite.me --><div style="background: #ffffff; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><table><tr><td><pre style="margin: 0; line-height: 125%">1</pre></td><td><pre style="margin: 0; line-height: 125%">print &#39;hello world!&#39;
</pre></td></tr></table></div>
