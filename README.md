## Using Spot Instances with AWS ParallelCluster and Amazon FSx for Lustre

Processing large amounts of complex data often requires leveraging a mix of different Amazon Elastic Compute Cloud (Amazon EC2) instance types. This is because some parts of a workflow benefit from a higher number of CPUs, and other parts benefit from a higher amount of memory. These types of computations also benefit from shared, high performance, scalable storage like Amazon FSx for Lustre. Right-sizing your compute fleet and storage to the computation needs is a way to control costs. Another way to save costs is to use Amazon EC2 Spot Instances, which can help to reduce EC2 costs up to 90% compared to On-Demand Instance pricing.

This post will guide you in the creation of a fault tolerance cluster allowing the automatic re-queue of the jobs in case of a Spot interruption. In addition, Spot Instances can decrease the cost of your jobs by up to 90% over On-Demand Instances. We will explain how to configure AWS ParallelCluster to run a Slurm cluster that is able to manage Spot Instance interruptions, automatically unmount the Amazon FSx for Lustre filesystem, and re-submit the interrupted jobs back into the queue so they run on new nodes of the cluster.

A Spot Instance is an instance that uses spare EC2 capacity that is available for less than the On-Demand Instance price. The hourly price for a Spot Instance is called a Spot price. The Spot price of each instance type in each Availability Zone is set by Amazon EC2, and is adjusted gradually based on long-term supply and demand. Additionally, Amazon EC2 can interrupt your instance when Amazon EC2 needs to fulfil demand for non-Spot Instance requests. When this event happens, Amazon EC2 provides an interruption notice, which gives the instance a two-minute warning before Amazon EC2 interrupts it.Amazon FSx for Lustre maintains a distributed and coherent state across both client and server instances, and delegates temporary access permissions to clients while they are actively-doing I/O and caching file system data. When a Spot Instance is reclaimed, the FSx for Lustre servers wait for the client’s reply for few minutes before evicting them. As the number of Spot Instances are reclaimed and shut down, the performance of the other-running FSx for Lustre clients can suffer.

To avoid the situation where a FSx for Lustre server is waiting multiple minutes for the terminated clients to reply to the server request, you can unmount the FSx for Lustre client before the Spot Instance shuts down. The documentation for FSx for Lustre covers how to work with Amazon EC2 Spot Instances and provides an example bash script.

With an HPC cluster running multiple instances, the unmount procedure must be managed at scale. Once a Spot Instance is reclaimed by Amazon EC2, the scheduler must shut down the whole job impacted and then your job must be re-submitted.

This functionality allows specific categories of jobs to be interrupted and resumed anytime. Some examples are Monte Carlo simulations, and checkpoint-able applications. Monte Carlo simulations are a type of computational algorithms that predicts a set of outcomes based on repeated random sampling. This class of algorithm can be restarted anytime without specific requirements. Checkpoint-able applications are another type of jobs that can save the status of the simulation and restart from the saved state.

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

