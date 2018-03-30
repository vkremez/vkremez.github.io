# VK Simple Debugger
C++ reusuables:

I. Create_Process function
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
II. Open Process function
<pre>
  <code class="c++">
HANDLE open_process(int PID)
	{
		printf("[*] Enter open_process() function...\n");
		dwProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, PID);
		return dwProcess;
	}
 </code>
</pre>
III. Open Thread function
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
IV. Enumerate threads
<pre>
  <code class="c++">
  std::vector<DWORD> enumerate_threads(void)
	{
		std::vector<DWORD> mylist;
		printf("[*] Enter enumerate_threads() function...\n");
		THREADENTRY32 thread_entry;
		thread_entry.dwSize = sizeof(THREADENTRY32);
		HANDLE snapshot = CreateToolhelp32Snapshot(TH32CS_SNAPTHREAD, static_cast<DWORD>(PID));
		if (snapshot)
		{
			BOOL thread_bool = Thread32First(snapshot, &thread_entry);
			do
			{
				if (thread_entry.th32OwnerProcessID == static_cast<DWORD>(PID))
				{
					//printf("Thread ID: 0x%04x\n", thread_entry.th32ThreadID);
					std::cout << "Thread ID: " << thread_entry.th32ThreadID << std::endl;
					mylist.push_back(thread_entry.th32ThreadID);
				}
			} while (Thread32Next(snapshot, &thread_entry));
		}
		CloseHandle(snapshot);
		return mylist;
	}	
 	</code>
</pre>
V. 
<pre>
  <code class="c++">
	CONTEXT get_thread_context(int thread_id, HANDLE h_thread)
	{
		printf("[*] Enter get_thread_context() function...\n");
		CONTEXT context;
		context.ContextFlags = CONTEXT_FULL | CONTEXT_DEBUG_REGISTERS;
		h_thread = open_thread(thread_id);
		GetThreadContext(h_thread, &context);
		return context;
	}
 	</code>
</pre>
