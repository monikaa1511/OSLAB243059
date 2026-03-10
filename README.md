# OSLAB243059
Operating System Lab exercise
# oslab3068
Operating System Lab exercise


ex 3
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t child_pid;

    child_pid = fork();

    if (child_pid < 0) {
        perror("Fork failed");
        return 1;
    }
    else if (child_pid == 0) {
        // Child
        printf("Child process: PID=%d\n", getpid());

        execl("/bin/ls", "ls", "-l", NULL);

        perror("execl failed");
        return 1;
    }
    else {
        // Parent
        printf("Parent process: PID=%d\n", getpid());
        wait(NULL);
        printf("Child process finished.\n");
    }

    return 0;
}


ex 4a

#include <stdio.h>

int main() {
    int n, i;
    int burst[10], wait[10], turn[10];
    float avg_w = 0, avg_t = 0;

    printf("Enter the number of processes: ");
    scanf("%d", &n);

    printf("Enter the burst time for each process:\n");
    for (i = 0; i < n; i++)
        scanf("%d", &burst[i]);

    wait[0] = 0;
    for (i = 1; i < n; i++)
        wait[i] = wait[i - 1] + burst[i - 1];

    for (i = 0; i < n; i++)
        turn[i] = wait[i] + burst[i];

    printf("\4bnGantt Chart:\n|");
    for (i = 0; i < n; i++)
        printf(" P%d |", i + 1);

    printf("\n0");
    for (i = 0; i < n; i++)
        printf("   %d", turn[i]);

    for (i = 0; i < n; i++) {
        avg_w += wait[i];
        avg_t += turn[i];
    }

    printf("\nAverage Waiting Time: %.2f", avg_w / n);
    printf("\nAverage Turnaround Time: %.2f", avg_t / n);

    return 0;
}


ex 4b
#include <stdio.h>

struct process {
    int id;
    int bt;      // Burst Time
    int wt;      // Waiting Time
    int tat;     // Turnaround Time
    int pr;      // Priority
};

void priorityScheduling(struct process p[], int n)
{
    struct process temp;

    // Sort by priority (smaller number = higher priority)
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            if (p[i].pr > p[j].pr) {
                temp = p[i];
                p[i] = p[j];
                p[j] = temp;
            }
        }
    }

    // Waiting time
    p[0].wt = 0;
    for (int i = 1; i < n; i++)
        p[i].wt = p[i-1].bt + p[i-1].wt;

    // Turnaround time
    for (int i = 0; i < n; i++)
        p[i].tat = p[i].wt + p[i].bt;

    float avgWT = 0, avgTAT = 0;

    printf("\nProcess\tBT\tPriority\tWT\tTAT\n");

    for (int i = 0; i < n; i++) {
        printf("P%d\t%d\t%d\t\t%d\t%d\n",
               p[i].id, p[i].bt, p[i].pr, p[i].wt, p[i].tat);

        avgWT += p[i].wt;
        avgTAT += p[i].tat;
    }

    printf("\nAverage Waiting Time = %.2f", avgWT/n);
    printf("\nAverage Turnaround Time = %.2f\n", avgTAT/n);
}

int main()
{
    int n;

    printf("Enter number of processes: ");
    scanf("%d", &n);

    struct process p[n];

    for (int i = 0; i < n; i++) {
        p[i].id = i + 1;
        printf("\nEnter Burst Time for P%d: ", i+1);
        scanf("%d", &p[i].bt);
        printf("Enter Priority for P%d: ", i+1);
        scanf("%d", &p[i].pr);
    }

    priorityScheduling(p, n);

    return 0;
}

4c
#include <stdio.h>

struct process {
    int id;
    int bt;      // Burst Time
    int wt;      // Waiting Time
    int tat;     // Turnaround Time
    int pr;      // Priority
};

void priorityScheduling(struct process p[], int n)
{
    struct process temp;

    // Sort by priority (smaller number = higher priority)
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            if (p[i].pr > p[j].pr) {
                temp = p[i];
                p[i] = p[j];
                p[j] = temp;
            }
        }
    }

    // Waiting time
    p[0].wt = 0;
    for (int i = 1; i < n; i++)
        p[i].wt = p[i-1].bt + p[i-1].wt;

    // Turnaround time
    for (int i = 0; i < n; i++)
        p[i].tat = p[i].wt + p[i].bt;

    float avgWT = 0, avgTAT = 0;

    printf("\nProcess\tBT\tPriority\tWT\tTAT\n");

    for (int i = 0; i < n; i++) {
        printf("P%d\t%d\t%d\t\t%d\t%d\n",
               p[i].id, p[i].bt, p[i].pr, p[i].wt, p[i].tat);

        avgWT += p[i].wt;
        avgTAT += p[i].tat;
    }

    printf("\nAverage Waiting Time = %.2f", avgWT/n);
    printf("\nAverage Turnaround Time = %.2f\n", avgTAT/n);
}

int main()
{
    int n;

    printf("Enter number of processes: ");
    scanf("%d", &n);

    struct process p[n];

    for (int i = 0; i < n; i++) {
        p[i].id = i + 1;
        printf("\nEnter Burst Time for P%d: ", i+1);
        scanf("%d", &p[i].bt);
        printf("Enter Priority for P%d: ", i+1);
        scanf("%d", &p[i].pr);
    }

    priorityScheduling(p, n);

    return 0;
}


ex 4d
#include <stdio.h>

void findWaitingTime(int bt[], int wt[], int n, int q)
{
    int rem_bt[n];

    for (int i = 0; i < n; i++)
        rem_bt[i] = bt[i];

    int t = 0;

    while (1) {
        int done = 1;

        for (int i = 0; i < n; i++) {
            if (rem_bt[i] > 0) {
                done = 0;

                if (rem_bt[i] > q) {
                    t += q;
                    rem_bt[i] -= q;
                } else {
                    t += rem_bt[i];
                    wt[i] = t - bt[i];
                    rem_bt[i] = 0;
                }
            }
        }

        if (done == 1)
            break;
    }
}

int main()
{
    int n, q;

    printf("Enter number of processes: ");
    scanf("%d", &n);

    int bt[n], wt[n], tat[n];

    printf("Enter burst times:\n");
    for (int i = 0; i < n; i++) {
        printf("P%d: ", i+1);
        scanf("%d", &bt[i]);
    }

    printf("Enter time quantum: ");
    scanf("%d", &q);

    findWaitingTime(bt, wt, n, q);

    float avgWT = 0, avgTAT = 0;

    printf("\nProcess\tBT\tWT\tTAT\n");

    for (int i = 0; i < n; i++) {
        tat[i] = bt[i] + wt[i];

        printf("P%d\t%d\t%d\t%d\n",
               i+1, bt[i], wt[i], tat[i]);

        avgWT += wt[i];
        avgTAT += tat[i];
    }

    printf("\nAverage Waiting Time = %.2f", avgWT/n);
    printf("\nAverage Turnaround Time = %.2f\n", avgTAT/n);

    return 0;
ex5 
#include <stdio.h>
#include <unistd.h>
#include <sys/shm.h>
#include <sys/ipc.h>
#include <sys/wait.h>

int main()
{
    int shmid;
    char *shmptr;

    shmid = shmget(2041, 32, 0666 | IPC_CREAT);

    if (fork() == 0) {
        // Child process — writing
        shmptr = shmat(shmid, NULL, 0);

        printf("Parent process writing:\n");

        for (int i = 0; i < 10; i++) {
            shmptr[i] = 'a' + i;
            putchar(shmptr[i]);
        }

        shmptr[10] = '\0';
        printf("\n");

        shmdt(shmptr);
    }
    else {
        wait(NULL);

        // Parent process — reading
        shmptr = shmat(shmid, NULL, 0);

        printf("Child process reading:\n");

        for (int i = 0; i < 10; i++)
            putchar(shmptr[i]);

        printf("\n");

        shmdt(shmptr);
        shmctl(shmid, IPC_RMID, NULL);
    }

    return 0;
}

ex 6
#include <stdio.h>
#include <pthread.h>
#include <semaphore.h>
#include <stdlib.h>

sem_t mutex;
int shared_resource = 0;

void *thread_function(void *arg)
{
    int i, val;
    long thread_id = (long)arg;

    for (i = 0; i < 5; i++) {
        sem_wait(&mutex);   // Lock the semaphore

        val = shared_resource;
        printf("Thread %ld: Read %d from shared resource\n", thread_id, val);

        val++;
        printf("Thread %ld: Writing %d to shared resource\n", thread_id, val);

        shared_resource = val;

        sem_post(&mutex);   // Unlock the semaphore
    }

    return NULL;
}

int main()
{
    pthread_t threads[3];
    int i, ret;

    // Initialize semaphore (1 = acts like mutex)
    sem_init(&mutex, 0, 1);

    // Create threads
    for (i = 0; i < 3; i++) {
        ret = pthread_create(&threads[i], NULL, thread_function, (void *)(long)i);
        if (ret != 0) {
            perror("pthread_create failed");
            return -1;
        }
    }

    // Wait for threads to finish
    for (i = 0; i < 3; i++) {
        ret = pthread_join(threads[i], NULL);
        if (ret != 0) {
            perror("pthread_join failed");
            return -1;
        }
    }

    printf("Final value of shared resource: %d\n", shared_resource);

    sem_destroy(&mutex);
    return 0;
}
test case 3
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

int main() {
    pid_t pid = fork();

    if (pid < 0) {
        // Fork failed
        printf("Fork Failed\n");
    }
    else if (pid == 0) {
        // Child process
        // Delay slightly so parent prints first
        sleep(1);
        printf("Child Process\n");
    }
    else {
        // Parent process prints immediately
        printf("Parent Process\n");
        // Wait for child to finish
        wait(NULL);
    }

    return 0;
}

test case 4
#include <stdio.h>

// Idle CPU Case
void idleCPUCase() {
    printf("=== Idle CPU Case ===\n");
    printf("Processes:\n");
    printf("P1 AT=0 BT=3\n");
    printf("P2 AT=5 BT=4\n");
    printf("Timeline:\n");
    printf("P1 runs from 0 to 3\n");
    printf("CPU Idle from 3 to 5\n");
    printf("P2 runs from 5 to 9\n");
    printf("Expected Output:\n");
    printf("Idle from 3 to 5\n");
    printf("Average Waiting Time = 0\n\n");
}

// Long Job Starvation (Priority Scheduling)
void priorityStarvationCase() {
    printf("=== Long Job Starvation (Priority Scheduling) ===\n");
    printf("Processes:\n");
    printf("P1 AT=0 BT=10 Priority=5\n");
    printf("P2 AT=1 BT=2 Priority=1\n");
    printf("P3 AT=2 BT=2 Priority=1\n");
    printf("Timeline:\n");
    printf("P2 runs from 1 to 3 (higher priority)\n");
    printf("P3 runs from 3 to 5 (higher priority)\n");
    printf("P1 runs last from 5 to 15\n");
    printf("Expected Output:\n");
    printf("P1 waits long → Starvation example\n\n");
}

// Large Burst Variation (SJF Scheduling)
void sjfCase() {
    printf("=== Large Burst Variation (SJF Scheduling) ===\n");
    printf("Processes:\n");
    printf("P1 AT=0 BT=20\n");
    printf("P2 AT=1 BT=2\n");
    printf("P3 AT=2 BT=1\n");
    printf("Timeline (Shortest Job First):\n");
    printf("P2 runs from 1 to 3\n");
    printf("P3 runs from 3 to 4\n");
    printf("P1 runs from 0 to 20 (after short jobs arrive)\n");
    printf("Expected Output:\n");
    printf("SJF gives minimum Average Waiting Time\n\n");
}

int main() {
    idleCPUCase();
    priorityStarvationCase();
    sjfCase();
    return 0;
}

test case5
#include <stdio.h>
#include <unistd.h>
#include <string.h>
#include <sys/types.h>
#include <sys/wait.h>

int main() {
    int fd[2];          // File descriptors for pipe
    pipe(fd);           // Create pipe

    if (fork() == 0) {
        // Child process
        close(fd[1]);   // Close write end
        char buf[50];
        read(fd[0], buf, sizeof(buf));
        printf("Child received: %s\n", buf);
        close(fd[0]);   // Close read end
    } else {
        // Parent process
        close(fd[0]);   // Close read end
        write(fd[1], "Hello Child", strlen("Hello Child") + 1);
        close(fd[1]);   // Close write end
        wait(NULL);     // Wait for child to finish
    }

    return 0;
}
ex:7

#include&lt;stdio.h&gt;
#include&lt;conio.h&gt;
#include&lt;stdlib.h&gt;
int p,r,m,z,k,buf,a[10],avail[10],max[10][10],alloc[10][10],need[10][10],i,j,top;
void get()
{
printf(&quot;\nENTER THE NUMBER OF PROCESSES:&quot;);
scanf(&quot;%d&quot;,&amp;p);
printf(&quot;\nENTER THE NUMBER OF RESOURCE TYPES:&quot;);
scanf(&quot;%d&quot;,&amp;r);
for(j=1;j&lt;=r;j++)
{
printf(&quot;\nENTER THE NUMBER OF RESOURCES FOR TYPE %d : &quot;,j);
scanf(&quot;%d&quot;,&amp;avail[j]);
}
for(i=1;i&lt;=p;i++)
{
printf(&quot;\nENTER THE MAXIMUM NUMBER OF RESOURCES REQUIRED FOR
PROCESS %d:\n&quot;,i);
for(j=1;j&lt;=r;j++)
{
printf(&quot;\nFor Resource type %d :&quot;,j);
scanf(&quot;%d&quot;,&amp;max[i][j]);
}}

26
printf(&quot;\nENTER THE ALLOCATED INSTANCES:\n&quot;);
for(i=1;i&lt;=p;i++)
{
printf(&quot;\nPROCESS %d:&quot;,i);
printf(&quot;\n-------------&quot;);
for(j=1;j&lt;=r;j++)
{
printf(&quot;\nResource Type - %d :&quot;,j);
scanf(&quot;%d&quot;,&amp;m);
if(m&lt;=avail[j])
{ alloc[i][j]=m;
avail[j]=avail[j]-m;
}
else
printf(&quot;\nALLOCATION EXCEEDS MAXIMUM. U CAN&#39;T ALLOCATE IT.&quot;);
}
}
}
void disp1()
{
printf(&quot;\nNEEDED RESOURCES:&quot;);
printf(&quot;\n-----------------&quot;);
for(i=1;i&lt;=p;i++)
{
printf(&quot;\nProcess %d:\t&quot;,i);
for(j=1;j&lt;=r;j++)
{
need[i][j]=max[i][j]-alloc[i][j];
printf(&quot; %d&quot;,need[i][j]);
}
}//for i
} //function
void seqnc()
{
top=1;
while(top&lt;=p)
{
for(i=1;i&lt;=p;i++)
{
buf=0;
z=0;
for(j=1;j&lt;=r;j++)
{ z=z+need[i][j];
if(need[i][j]&lt;=avail[j])
buf++;
}
if(buf==r&amp;&amp;z!=0)
{
a[top]=i;
top++;
for(k=1;k&lt;=r;k++)

27

{
avail[k]=avail[k]+alloc[i][k];
need[i][k]=0;
}//for
} //if(buf==r)
} //for i
}//while
}//function
void disp2()
{
printf(&quot;\nThe Sequence of allocation to the processes:&quot;);
for(i=1;i&lt;=p;i++)
printf(&quot; %d&quot;,a[i]);
}
void main()
{
clrscr();
get();
disp1();
seqnc();
disp2();
getch();
}

ex:8

#include <stdio.h>

int main()
{
    int found,flag,l;
    int p[10][10],c[10][10];
    int tp,tr;
    int i,j,k=1;
    int m[10],r[10],a[10],temp[10];
    int sum=0;

    printf("Enter total no of processes: ");
    scanf("%d",&tp);

    printf("Enter total no of resources: ");
    scanf("%d",&tr);

    printf("Enter claim (Max. Need) matrix\n");
    for(i=1;i<=tp;i++)
    {
        printf("process %d:\n",i);
        for(j=1;j<=tr;j++)
        scanf("%d",&c[i][j]);
    }

    printf("Enter allocation matrix\n");
    for(i=1;i<=tp;i++)
    {
        printf("process %d:\n",i);
        for(j=1;j<=tr;j++)
        scanf("%d",&p[i][j]);
    }

    printf("Enter resource vector (Total resources):\n");
    for(i=1;i<=tr;i++)
    scanf("%d",&r[i]);

    printf("Enter availability vector (available resources):\n");
    for(i=1;i<=tr;i++)
    {
        scanf("%d",&a[i]);
        temp[i]=a[i];
    }

    for(i=1;i<=tp;i++)
    {
        sum=0;
        for(j=1;j<=tr;j++)
        sum+=p[i][j];

        if(sum==0)
        {
            m[k]=i;
            k++;
        }
    }

    for(i=1;i<=tp;i++)
    {
        for(l=1;l<k;l++)
        {
            if(i!=m[l])
            {
                flag=1;

                for(j=1;j<=tr;j++)
                {
                    if(c[i][j]>temp[j])
                    {
                        flag=0;
                        break;
                    }
                }

                if(flag==1)
                {
                    m[k]=i;
                    k++;

                    for(j=1;j<=tr;j++)
                    temp[j]+=p[i][j];
                }
            }
        }
    }

    printf("deadlock causing processes are:\n");

    for(j=1;j<=tp;j++)
    {
        found=0;

        for(i=1;i<k;i++)
        {
            if(j==m[i])
            found=1;
        }

        if(found==0)
        printf("%d ",j);
    }

    return 0;
}
