# VK Simple Debugger
C++ reusuables:

1. Create_Process function
<pre>
  <code class="c++">
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
  </code>
</pre>
2. Open Process function
HANDLE open_process(int PID)
	{
		printf("[*] Enter open_process() function...\n");
		dwProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, PID);
		return dwProcess;
	}
 </code>
</pre>
3. Open Thread
<pre>
  <code class="c++">
HANDLE open_thread(int thread_id)
	{
		printf("[*] Enter open_thread() function...\n");
		HANDLE dwThread = OpenProcess(THREAD_ALL_ACCESS, FALSE, thread_id);
		return dwThread;
	}
 </code>
</pre>
