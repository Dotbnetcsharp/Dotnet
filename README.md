public class TrackedStatus : BaseEntity
{
    public string Status { get; set; } = string.Empty;
    public string? ErrorTitle { get; set; }
    public string? ErrorMessage { get; set; }
    public string? ErrorData { get; set; }
    public string? CountyFips { get; set; }
    public string? StateCode { get; set; }
    public string? ParentId { get; set; }
    public string? DocumentsSearchId { get; set; }
    public string? SearchType { get; set; }
    public DateTime? DateFrom { get; set; }
    public DateTime? DateThru { get; set; }
    public string? Branch { get; set; }
    public string? User { get; set; }
    public bool? IsMergeResult { get; set; }
    public Guid? MergeSearchId { get; set; }
    public Guid? ParentSearchId { get; set; }
    public Guid? MatchCorrelationId { get; set; }
    public string? ClientIp { get; set; }

    public APNSearch? APNSearch { get; set; }

    public static void CreateModel(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<TrackedStatus>().ToTable("search_items");

        modelBuilder.Entity<TrackedStatus>().Property(e => e.Status).HasColumnName("status");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.ErrorTitle).HasColumnName("error title");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.ErrorMessage).HasColumnName("error message");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.ErrorData).HasColumnName("error data");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.CountyFips).HasColumnName("county fips");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.StateCode).HasColumnName("state code");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.ParentId).HasColumnName("parent id");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.DocumentsSearchId).HasColumnName("documents search id");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.SearchType).HasColumnName("search type");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.DateFrom).HasColumnName("search from date");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.DateThru).HasColumnName("search thru date");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.Branch).HasColumnName("branch");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.User).HasColumnName("user");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.IsMergeResult).HasColumnName("IsMergeResult");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.MergeSearchId).HasColumnName("mergeSearchId");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.ParentSearchId).HasColumnName("parentSearchId");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.MatchCorrelationId).HasColumnName("matchCorrelationId");
        modelBuilder.Entity<TrackedStatus>().Property(e => e.ClientIp).HasColumnName("clientip");

        modelBuilder.Entity<TrackedStatus>()
            .HasOne(s => s.APNSearch)
            .WithOne(a => a.SearchParent)
            .HasForeignKey<APNSearch>(a => a.SearchItemId);
    }
}

public class APNSearch : BaseEntity
{
    public Guid SearchItemId { get; set; }
    public TrackedStatus SearchParent { get; set; } = null!;
    public string SearchedAPN { get; set; } = string.Empty;
    public string PropertyId { get; set; } = string.Empty;

    public static void CreateModel(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<APNSearch>().ToTable("apn_searches");

        modelBuilder.Entity<APNSearch>().Property(e => e.SearchedAPN).HasColumnName("searched apn");
        modelBuilder.Entity<APNSearch>().Property(e => e.PropertyId).HasColumnName("property id");
        modelBuilder.Entity<APNSearch>().Property(e => e.SearchItemId).HasColumnName("search item_id");

        modelBuilder.Entity<APNSearch>()
            .HasOne(a => a.SearchParent)
            .WithOne(s => s.APNSearch)
            .HasForeignKey<APNSearch>(a => a.SearchItemId);

        CreateBaseModel<APNSearch>(modelBuilder); // Ensure this method is defined in BaseEntity or elsewhere
    }
}

