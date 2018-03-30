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
V. Get Thread Context function
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
VI. Obtain raw resource function
<pre>
 <code class="c++">
BYTE* get_raw_payload(OUT SIZE_T &res_size)
{
    HMODULE hInstance = GetModuleHandle(NULL);
    HRSRC res = FindResource(hInstance, MAKEINTRESOURCE(MY_RESOURCE), RT_RCDATA);
    if (!res) return NULL;

    HGLOBAL res_handle  = LoadResource(NULL, res);
    if (res_handle == NULL) return NULL;

    BYTE* res_data = (BYTE*) LockResource(res_handle);
    res_size = SizeofResource(NULL, res);

    BYTE* out_buf = (BYTE*) VirtualAlloc(NULL,res_size, MEM_COMMIT | MEM_RESERVE, PAGE_EXECUTE_READWRITE);
    memcpy(out_buf, res_data, res_size);

    FreeResource(res_handle);
    return out_buf;
}
 </code>
</pre>
VII. 
