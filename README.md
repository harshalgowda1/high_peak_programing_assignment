# high_peak_programing_assignment
import java.util.*;
public class JobSelection 
{
	public static void main(String[] args) {
	    Scanner scanner = new Scanner(System.in);

	    System.out.println("Enter the number of Jobs");
	    int n = scanner.nextInt();

	    Job[] jobs = new Job[n];
	    for (int i = 0; i < n; i++) {
	        System.out.println("Enter job start time, end time, and earnings");
	        int startTime = scanner.nextInt();
	        int endTime = scanner.nextInt();
	        int profit = scanner.nextInt();
	        jobs[i] = new Job(startTime, endTime, profit);
	    }

	    int[] result = getRemainingJobsAndEarnings(jobs);
	    System.out.println("The number of tasks and earnings available for others");
	    System.out.println("Task: " + result[0]);
	    System.out.println("Earnings: " + result[1]);
	}

	public static int[] getRemainingJobsAndEarnings(Job[] jobs) {
	    Arrays.sort(jobs, Comparator.comparing(Job::getEndTime)); // sort jobs by end time
	    int n = jobs.length;
	    int[] dp = new int[n];
	    dp[0] = jobs[0].profit;
	    for (int i = 1; i < n; i++) {
	        int includingProfit = jobs[i].profit;
	        int latestNonConflictingJobIndex = findLatestNonConflictingJobIndex(jobs, i);
	        if (latestNonConflictingJobIndex != -1) {
	            includingProfit += dp[latestNonConflictingJobIndex];
	        }
	        dp[i] = Math.max(dp[i-1], includingProfit);
	    }
	    int maxProfit = dp[n-1];
	    int numRemainingJobs = n;
	    for (int i = n-1; i >= 0; i--) {
	        if (dp[i] != maxProfit) {
	            numRemainingJobs = n - i - 1;
	            break;
	        }
	    }
	    int remainingEarnings = getTotalEarnings(jobs) - maxProfit;
	    return new int[] { numRemainingJobs, remainingEarnings };
	}

	private static int findLatestNonConflictingJobIndex(Job[] jobs, int i) {
	    for (int j = i-1; j >= 0; j--) {
	        if (jobs[j].endTime <= jobs[i].startTime) {
	            return j;
	        }
	    }
	    return -1;
	}

	private static int getTotalEarnings(Job[] jobs) {
	    int totalEarnings = 0;
	    for (Job job : jobs) {
	        totalEarnings += job.profit;
	    }
	    return totalEarnings;
	}

	private static class Job {
	    int startTime;
	    int endTime;
	    int profit;

	    public Job(int startTime, int endTime, int profit) {
	        this.startTime = startTime;
	        this.endTime = endTime;
	        this.profit = profit;
	    }

	    public int getEndTime() {
	        return endTime;
	    }
	}
}
